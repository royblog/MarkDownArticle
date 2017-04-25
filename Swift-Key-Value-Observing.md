---
title: Swift Key-Value Observing
date: 2017-04-20 12:57:46
tags: [iOS, Swift, KVO]
---

KVO（Key-Value Observing）是为了其他不同实例对当前的某个属性 (严格来说是 keypath) 进行监听时使用的。其他实例可以充当一个订阅者的角色，当被监听的属性发生变化时，订阅者将得到通知。
这是一个很强大的属性，通过 KVO 我们可以实现很多松耦合的结构，使代码更加灵活和强大：像通过监听 model 的值来自动更新 UI 的绑定这样的工作，基本都是基于 KVO 来完成的。
在 Swift 中我们也是可以使用 KVO 的，但是仅限于在 NSObject 的子类中。这是可以理解的，因为 KVO 是基于 KVC (Key-Value Coding) 以及动态派发技术实现的，而这些东西都是 Objective-C 运行时的概念。另外由于 Swift 为了效率，默认禁用了动态派发，因此想用 Swift 来实现 KVO，我们还需要做额外的工作，那就是将想要观测的对象标记为 dynamic。

### 官方代码
``` bash
class MyObjectToObserve: NSObject {
    dynamic var myDate = NSDate()
    func updateDate() {
        myDate = NSDate()
    }
}

private var myContext = 0

class MyObserver: NSObject {
    var objectToObserve = MyObjectToObserve()
    override init() {
        super.init()
        objectToObserve.addObserver(self, forKeyPath: #keyPath(MyObjectToObserve.myDate), options: .new, context: &myContext)
    }

    override func observeValue(forKeyPath keyPath: String?, of object: Any?, change: [NSKeyValueChangeKey : Any]?, context: UnsafeMutableRawPointer?) {
        if context == &myContext {
            if let newValue = change?[.newKey] {
                print("Date changed: \(newValue)")
            }
        } else {
            super.observeValue(forKeyPath: keyPath, of: object, change: change, context: context)
        }
    }

    deinit {
        objectToObserve.removeObserver(self, forKeyPath: #keyPath(MyObjectToObserve.myDate), context: &myContext)
    }
}

let obj = MyObserver()
obj.objectToObserve.updateDate()
```
首先是 Swift 的 KVO 需要依赖的东西比原来多。在 Objective-C 中我们几乎可以没有限制地对所有满足 KVC 的属性进行监听，而现在我们需要属性有 dynamic 进行修饰。大多数情况下，我们想要观察的类不一定是 dynamic 修饰的 (除非这个类的开发者有意为之，否则一般也不会有人愿意多花功夫在属性前加上 dynamic，因为这毕竟要损失一部分性能)，并且有时候我们很可能也无法修改想要观察的类的源码。遇到这样的情况的话，一个可能可行的方案是继承这个类并且将需要观察的属性使用 dynamic 进行重写。比如刚才我们的 MyObjectToObserve 中如果 date 没有 dynamic 的话，我们可能就需要一个新的 OtherMyObjectToObserve 了：
``` bash
class MyObjectToObserve: NSObject {
    var myDate = NSDate()
    func updateDate() {
        myDate = NSDate()
    }
}

class OtherMyObjectToObserve: MyObjectToObserve {
    dynamic override var myDate: NSDate {
        get {
            return super.myDate
        }
        set {
            super.myDate = newValue
        }
    }
}
```

[KVO](http://swifter.tips/kvo/)代码使用的是苹果官方示例



