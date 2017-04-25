---
title: Swift Key-Value Coding
date: 2017-04-20 11:09:58
tags: [iOS, Swift, KVC]
categories: Swift
---

KVC (Key-Value Coding)， 它是一种用间接方式访问类的属性的机制。在 Swift 中为一个类实现 KVC 的话，需要让它继承自 NSObject。
### Code

``` bash
class Person: NSObject {
    var firstName: String
    var lastName: String

    init(firstName: String, lastName: String) {
        self.firstName = firstName
        self.lastName = lastName
    }
} 
let roy = Person(firstName: "roy", lastName: "wang")

```
直接引用属性：

``` bash
print(roy.lastName)
```
通过KVC机制访问：
``` bash
if let newValue = roy.value(forKey: "lastName") {
    print(newValue)
}
```
如果使用KVC获取一个不存在的key，会导致程序崩溃。找不到key，会调用valueForUndefinedKey，我们可以重新此方法：
``` bash
class Person: NSObject {
    var firstName: String
    var lastName: String

    init(firstName: String, lastName: String) {
        self.firstName = firstName
        self.lastName = lastName
    }

    override func value(forUndefinedKey key: String) -> Any? {
        return ""
    }
}
```
KVC 除了可以用单个的 key 来访问单个属性，还提供了一个叫做 keyPath 的东西。所谓 keyPath，就比如你的属性本身也有自己的属性，那么想引用这个属性，就需要用到 keyPath。咱们用一个示例来说明：
``` bash
class Province: NSObject {
    var proName: String
    init(proName: String) {
        self.proName = proName
    }
}

class Country: NSObject {
    var conName: String
    var conProvince: Province
    init(conName: String, conProvince: Province) {
    self.conName = conName
        self.conProvince = conProvince
    }
}

var con = Country(conName: "CN", conProvince: Province(proName: "HenanProvince"))

print(con.value(forKeyPath: "conProvince.proName"))
```
KVC 定义了使用 valueForKey获取属性，同样也提供了设置属性的方法 setValue:forKey
``` bash
con.setValue("US", forKey: "conName")
```
### 标量值
所谓标量值（Scalar Type）就是简单的类型的属性，比如int，float，这些非对象属性，关于标量值在KVC中有些地方需要注意：
``` bash
class Person: NSObject {
    var firstName: String
    var lastName: String
    var age: Int

    init(firstName: String, lastName: String, age: Int) {
        self.firstName = firstName
        self.lastName = lastName
        self.age = age
    }
}
let roy = Person(firstName: "roy", lastName: "wang", age: 14)
roy.setValue(nil, forKey: "age")
```
age是一个简单标量值，标量值不能为nil,虽然setValue 方法可以接受任何类型的参数作为值的设置，但 age 的底层存储确实标量值，因此我们执行上面那条 setValue 语句的时候必然会造成程序的崩溃。
那么我们除了注意避免将 nil 传递给底层存储是标量类型的属性之外，还有没有其他方法呢？ 
KVC 为我们提供了一个 setNilValueForKey 方法，每当我们要将 nil 设置给一个 key 的时候，这个方法就会被调用，所以我们可以修改一下 Person 类的定义：

```  bash
class Person: NSObject {
    var firstName: String
    var lastName: String
    var age: Int

    init(firstName: String, lastName: String, age: Int) {
        self.firstName = firstName
        self.lastName = lastName
        self.age = age
    }

    override func setNilValueForKey(_ key: String) {
        if key == "age" {
            self.setValue(18, forKey: "age")
        }
    }
}
```

### FAQ
**实现KVC，为什么要继承 NSObject**
> KVC 机制是由一个协议 NSKeyValueCoding 定义的。NSObject 帮我们实现了这个协议，所以 KVC 核心的逻辑都在 NSObject 中，我们继承 NSObject 才能让我们的类获得 KVC 的能力。(理论上说，如果你遵循 NSKeyValueCoding 协议的接口，其实也可以自己实现 KVC 的细节，完全行得通。
















