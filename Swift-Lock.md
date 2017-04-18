---
title: Swift Lock
date: 2017-04-18 16:48:52
tags: [iOS, Swift, Lock, Thread]
categories: Swift
---
在不同线程中安全的访问同一资源，需要为资源加上线程锁，否则多个线程访问同一资源，会导致结果不可预测。
在OC中使用@synchronized为资源加锁，但是在Swift中这个方法已经不存在了。@synchronized幕后所做的工作是调用objc_sync中的objc_sync_enter和objc_sync_exit方法。
在Swift中lock一个变量可以使用：
``` bash
func myMethod(anObj: AnyObject!) {
objc_sync_enter(anObj)
//在enter 和 exit 之间 anObj不会被其它线程改变
objc_sync_exit(anObj)
```
加锁和解锁都需要消耗性能，我们不可能为所有方法都加上锁，并且在App中涉及多线程部分有限，也没有必要为所有东西加锁。过多的锁没有意义，不但会消耗性能，也有可能会导致死锁问题。在处理多线程时，尽量保持简单。

[Swift Lock](http://swifter.tips/lock/)
