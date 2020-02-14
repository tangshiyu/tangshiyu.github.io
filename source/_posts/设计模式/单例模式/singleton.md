---
title: 单例模式
description: 单例模式通过Go来实现
tags: 设计模式
categories: 设计模式
date: 2020-02-13 12:08:40
---

![test](./test.png)

单例模式是我们平常开发中用的最多设计模式之一，单例有很多级别上的，例如函数上的单例，进程上的单例，下面讲的是代码中的单例，只要了解单例的思想即可，语言知识实现工具，下面讲解如下几种类型单例模式

- 饿汉模式
- 懒汉模式
- 懒汉模式(加锁)
- 懒汉模式(双重检查加锁)
- sync.Once实现



相关代码如下:

```go
import (
	"fmt"
	"sync"
)

type SingleTon struct {
}

var singleTon *SingleTon

var mu sync.Mutex

//懒汉模式
//还有一种饿汉式，就是一开始就初始化singleTon,实现如下
//func init() {
// singleTon = &SingleTon{}
//}
func NewSingleTon() *SingleTon {
   if singleTon == nil {
      singleTon = &SingleTon{}
   }
   return singleTon
}

// 懒汉加锁
func NewSingleTonByLock() *SingleTon {
   mu.Lock()
   defer mu.Unlock()

   if singleTon == nil {
      singleTon = &SingleTon{}
   }
   return singleTon
}

// 懒汉加检查锁
func NewSingleTonByLock2() *SingleTon {
   if singleTon == nil {
      mu.Lock()
      defer mu.Unlock()

      if singleTon == nil {
         singleTon = &SingleTon{}
      }
   }
   return singleTon
}

// 通过sync.Once实现
// 看过Once包的知道，实现很简单通过加锁实现，通过done标记位来判断，运行过一次通过原子操作将done置为1
var once sync.Once

func NewSingleTonByOnce() *SingleTon {
   once.Do(func() {
      singleTon = &SingleTon{}
   })

   return singleTon
}
```