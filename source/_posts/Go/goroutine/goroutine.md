---
title: Golang 中的 goroutine 理解
description: 
toc: true
tags: Go
categories: Go
date: 2020-02-16 10:22:56

---



## 什么是goroutine

### 相关概念

- 进程

  进程是系统进行资源分配的和调度的基本单元，具有独立功能的程序在某个数据集合上的一次执行过程

- 线程

​       线程是进程的一个执行实体或者执行单元，是比进程更小的且能独立运行的基本单元，一个进程至少会有一个线程

- 协程

  协程又被称作为微协程，纤程。

  协程相对独立有自己的上下文，由线程拉起，和线程相比协程避免了无意义的调度因此提高了性能，协程相对线程而言节约的是CPU的切换时间，但需要开发人员自己通过代码去调度kong

- goroutine

​    goroutine是Go语言的协程实现，从调度上讲goroutine的调度开销远远小于线程调度开销，不同的是Go在runtime和系统调用等方面对goroutine做了封装处理，goroutine不完全是受开发人员控制，一定程度上由Go runtime管理, 当某个goroutine受阻塞时，会让出CPU资源给其他的goroutine, Golang 的一大特色就是从语言层面原生支持协程，在函数或者方法前面加 go关键字就可创建一个协程。



## CSP并发模型

​          这里简单介绍下CSP，后面专门写一篇详细介绍CSP模型, CSP(communicating sequential processes)它不同与多线程通过共享内存来通信，像Java中共享内存通信时需要考虑线程安全问题,`原子性`、`有序性`、`可见性`。CSP模型讲究的是通过通信来共享内存，这也是Go语言特有的并发模型，CSP模型主要通过Go的goroutine和channel来实现。

## GPM模型

这里也是简单了解和介绍，后面将详细介绍

G: Goroutine ，本质上一种轻量级的线程

P: Processor, 代表了M所需要上下文环境，处理用户级代码逻辑的处理器，这里是一个抽象的概念并非真正的CPU，当P有任务时需要创建或者唤醒一个系统线程来执行它队列里面任务，所以需要P与M的绑定形成一个执行单元

M: Machine, 关联系统的一个内核线程，每次创建一个M时候都会有一个底层线程创建，所有的G任务最后都是在G上面运行

它们之间的关系如下：

​    一个M(Machine)对应一个系统内核线程，也会连接一个上下文P(Processor)，一个上下文P会关联多个G(Goroutine )

## goroutine实际应用

​    Go的并发写起来非常容器不像其他语言，只需要一个go关键字就可以搞定，例如:`go work()`, 在启动一个goroutine，一定需要知道它时何时关闭的，并且要节约使用，考虑它的必要性避免滥用，使用goroutine会给程序带来风险特别是死锁，对于后面问题排除也会带来很大的麻烦

### sync.WaitGroup

​    当我们启动一个goroutine时候，我们必须考虑它何时结束，但运行时间较长的时候，不做任何操作，这时候如果main主进程运行结束，会导致进程直接退出，不会考虑启动的goroutine有没有结束，甚至可能goroutine还没有起来，main已经运行完了，有必要的时候需要等待启动的goroutine运行完再退出，有人可能马上想到用sleep的方式，sleep不能准确估计goroutine运行时间，甚至可能会导致进程一直卡住，这里我们需要一个管理者，这个管理者就是sync.WaitGroup

案例代码如下:

```go
package main

import (
   "fmt"
   "sync"
   "time"
)

func work(data int) {
   fmt.Println(data)
   time.Sleep(time.Duration(1) * time.Second)
}


func main() {
   gNum := 5
   var wg = sync.WaitGroup{}
   wg.Add(gNum)
   for i := 1; i <= gNum; i++ {
      go func(w sync.WaitGroup, data int) {
         work(data)
         wg.Done()
      }(wg, i)
   }
   wg.Wait()
}
```

运行结果如下:

```shell
5
1
2
4
3

Process finished with exit code 0
```



使用的时候一定要保证设置等待的goroutine，最后一定要Done关闭掉不然就会导致死锁



### 使用channel传递信息

00



### sync.Pool



## 





