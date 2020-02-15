---
title: 代理模式
description: 
tags: 设计模式
categories: 设计模式
date: 2020-02-15 10:08:40		
---





​    代理模式为其他对象提供一种代理以控制对这个对象的访问权限，它主要解决直接访问对象带来的问题，例如公司环境访问通过堡垒机代理访问生产环境权限，避免了生成环境权限全部开发的局面，只需要开放相应的权限给开发者即可，注意它不需要更改原始接口的内容

​     例如下面堡垒机，在原始虚拟机VM上做权限控制和审计功能

```go
package main

import (
   "errors"
   "fmt"
)

// 原始虚拟机拥有读写和执行权限
type VM struct {
}

func (vm *VM) Write() {
   fmt.Println("Permission to write....")
}

func (vm *VM) Read() {
   fmt.Println("Permission to Read.....")
}

func (vm *VM) execute() {
   fmt.Println("Permission to execute.....")
}

// 堡垒机
type AccessGateway struct {
   v *VM
}

// 堡垒机可以做相关权限控制,和审计功能
func NewAccessGateWay(username string) (*AccessGateway, error) {
   if username != "Yu" {
      return nil, errors.New("Username not enough permissions")
   }
   fmt.Printf("[%s] login into access gate wasy......\n", username)
   return &AccessGateway{&VM{}}, nil
}

func (ag *AccessGateway) Read() {
   ag.v.Read()
}

func (ag *AccessGateway) Write() {
   ag.v.Write()
}

func main() {
   var accessGateway, err = NewAccessGateWay("Yu")
   if err != nil {
      fmt.Printf("Login into access gateway meet err! %v", err)
      return
   }
   accessGateway.Write()
   accessGateway.Read()
}
```



输出结果如下:

```powershell
[Yu] login into access gate wasy......
Permission to write....
Permission to Read.....

Process finished with exit code 0
```