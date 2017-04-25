---
title: Swift Extension
date: 2017-04-24 15:43:58
tags: [iOS, Swift, Extension]
categories: Swift
---
扩展给已经存在的类，结构体，枚举类型和协议增加新的特性。
* 增加计算实例属性和计算类型属性
* 定义实例方法和类型方法
* 提供新的初始化器
* 定义下标
* 定义和使用新的内置类型
* 让一个存在的类型服从一个协议

#### 扩展语法
类似Objective-C中的Category，不同的是，Extension没有名字。扩展可以增加新的功能，但是不能覆盖已有的功能。
``` bash
extension someType {
    // new functionality to add to SomeType goes here
}
```
Extension能拓展存在的类型使其遵守一个或多个协议。
``` bash
extension someType: SomeProtocol, AnotherProtocol {
	// implementation of protocol requirements goes here
}
```

#### 计算属性拓展
扩展可以给已经存在的类型增加计算实例属性和计算类型属性，如以下扩展了Double类型：
``` bash
extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
```

#### 构造方法拓展
扩展能添加新的构造器，类只能能拓展便利构造期，不能扩展指定构造器。
``` bash
struct Size {
    var width = 0.0
    var height = 0.0
}

struct Point {
    var x = 0.0
    var y = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
}

let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0), size: Size(width: 5.0, height: 5.0))
```
我们可以为Rect拓展一个构造期(便利构造期)：
``` bash
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

#### 方法扩展
``` bash
extension Int {
    func repetitions(task:() -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}
3.repetitions {
    print("Hello")
}
```
结果为：
``` bash
Hello
Hello
Hello
```

#### 可变实例方法扩展
扩展增加的实例方法可以修改实例本身。结构体和枚举类型中的方法如果想要修改实例本身或者属性的话需要用mutating来修饰方法，所以扩展这样的方法也需要加mutating。
``` bash
extension Int{
    mutating func square() {
        self = self * self
        print(self)
    }
}

var someInt = 3
someInt.square()
```
结果为：
``` bash
9
```

#### 下标
扩展可以给存在的类型增加新的下标，顺序从右往左：
``` bash
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}
345[0]
```
结果为：
``` bash
5
```
#### 内置类型
拓展能为现存的类，结构体，枚举添加内置类型
下面的例子为Int添加一个新的枚举类型Kind，Kind包括正，零，负三种状态。
``` bash
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}

func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
```
结果为：
``` bash
+ + - 0 - 0 + 
```







