---
title: Swift Singleton
date: 2017-04-19 14:16:20
tags: [iOS, Swift, Singleton]
categories: Swift
---

单例必须是唯一的，在程序生命周期中只能存在一个这样的实例。
为保证单例的唯一性，单例类的初始化方法必须是私有的。这样就可以避免其他对象通过单例类创建额外的实例。
为保证在整个程序的生命周期中值有一个实例被创建，单例必须是线程安全的。

在Swift中单例实现比较简单：
``` bash
class TheOneAndOnly {
    static let shareInstance = TheOneAndOnly()
    private init() {}
}
```
全局变量（还有结构体和枚举体的静态成员）的Lazy初始化方法会在其被访问的时候调用一次。类似于调用'dispatch_once'以保证其初始化的原子性。这样就有了一种很酷的'单次调用'方式：只声明一个全局变量和私有的初始化方法即可。
