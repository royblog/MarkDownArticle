---
title: Swift Protocol
date: 2017-04-26 10:04:48
tags: [iOS, Swift, Protocol]
categories: Swift
---
#### 官方概述
> 协议定义了方法，属性和其它要求的蓝图，以适应特定的任务和功能。协议能够被类、结构体、枚举来实现，满足协议要求的类、枚举、结构体被称为协议的遵守者。[翻译官方文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267)（翻译可能有纰漏）

#### 我的理解：
> 我们可以定义一些类和方法，只定义不实现，类似于定义了一些接口，但是不处理接口的实现，我们把这些定义的集合暂且称之为协议。类，结构体，枚举等可以遵守这些协议，遵守协议可以获取定义的方法和属性，有一点点类似继承，但是又有很大不同，协议没有方法实现，只有定义，这有点类似于C++的抽象基类（Abstract Class）。简而言之，协议只定义不实现，可以被其它（类，结构体，枚举）遵守和实现。(理解可能会曲解原意)

***
#### 协议语法
定义协议非常简单，有点类似于类、结构体、枚举的定义
``` bash
protocol SomeProtocol {
    // Protocol definition goes here
}
```
自定义类型声明采用特定的协议，将协议名称置于类型名之后，用冒号分隔开，作为他们定义的一部分，可以列举多个协议，中间用逗号隔开。
``` bash
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```
如果一个类继承父类，将父类放在遵守的协议之前，使用逗号分隔。
``` bash
class SomeStructure: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```
#### 属性要求
协议要求声明属性声明为变量属性，使用前缀 var 关键字，gettable and settable(可读写) 属性在声明后写 { get set }， gettable 声明类型后写 { get }
``` bash
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```
若果是类成员，使用static修饰
``` bash
protocol AnotherProtocol {
    static var someTypeProperty: Int { get set }
}
```
定义一个协议，其中有一个属性fullName，定义一个结构体，遵守协议
``` bash
protocol FullyNamed {
    var fullName: String { get }
}

struct Person: FullyNamed {
    var fullName: String
}

let john = Person(fullName: "John Appleseed")
print(john.fullName)
```
结果是：
``` bash
John Appleseed
```
定义一个更复杂的类，同样遵守FullyNamed协议
``` bash
class Starship: FullyNamed {
    var prefix: String?
    var name : String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}

var ss1 = Starship(name: "耀国", prefix: "王")
print(ss1.fullName)
var ss2 = Starship(name: "耀国")
print(ss2.fullName)
```
结果是：
``` bash
王 耀国
耀国
```
#### 方法要求
可以在协议中定义方法，如例所示：
``` bash
protocol RandomNumberGenerator {
    func random() -> Double
}
```
类方法使用static修饰：
``` bash
protocol SomeProtocol {
    static func someTypeMethod()
}
```
看下面的例子：
``` bash
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c).truncatingRemainder(dividingBy: m))
        return lastRandom / m
    }
}

let generator = LinearCongruentialGenerator()
print("Here is a random number: \(generator.random()))")
print("And another one: \(generator.random())")
```
#### 突变方法要求
方法修改实例类型称为突变方法，在结构体和枚举方法中修改需要给方法加上mutating(class不用添加)
``` bash
protocol Togglable {
    mutating func toggle()
}
```
在枚举中实现此协议
``` bash
enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}

var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
```
#### 构造要求
必须使用required标记构造实现，使用 required 修饰符可以确保所有子类也必须提供此构造器实现，从而也能符合协议。 如果类已经被标记为 final，那么不需要在协议构造器的实现中使用 required 修饰符，因为 final 类不能有子类。关于 final 修饰符的更多内容，请参见防止重写。 

``` bash
protocol SomeProtocol {
    init(someParameter: Int)
}

class SomeClass: SomeProtocol {
     required init(someParameter: Int) {
        // initializer implementation goes here
    }
}

let c = SomeClass(someParameter: 12)
```
如果一个子类重写了父类的指定构造器，并且该构造器满足了某个协议的要求，那么该构造器的实现需要同时标注 required 和 override 修饰符： 
``` bash
protocol SomeProtocol {
    init()
}

class SomeSuperClass {
    init() {
        
    }
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
    required override init() {
        
    }
}
```
#### 协议作为类型
尽管协议本身并未实现任何功能，但是协议可以被当做一个成熟的类型来使用。 
使用场景：
* 作为函数、方法或构造器中的参数类型或返回值类型 
* 作为常量、变量或属性的类型 
* 作为数组、字典或其他容器中的元素类型 

> 协议是一种类型，因此协议类型的名称应与其他类型（例如 Int，Double，String）的写法相同，使用大写字母开头的驼峰式写法，例如（FullyNamed 和 RandomNumberGenerator）。 

协议作为类型使用例子：
``` bash
protocol RandomNumberGenerator {
    func random() -> Double
}

class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c).truncatingRemainder(dividingBy: m))
        return lastRandom / m
    }
}

class Dice {
    let sides: Int

    let generator: RandomNumberGenerator

    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    
    func roll() -> Int {
        return Int(generator.random() * Double(sides))
    }
}

var d = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 {
    print("Random dice roll is \(d.roll())")
}
```
#### 通过扩展增加协议一致性
即便无法修改源代码，依然可以通过扩展令已有类型遵循并符合协议。扩展可以为已有类型添加属性、方法、下标以及构造器，因此可以符合协议中的相应要求。
例如为声明一个协议，并使得Dice遵守该协议：
``` bash
protocol TextRepresentable {
    var textualDescription: String { get }
}

extension Dice: TextRepresentable {
    var textualDescription: String {
        return "A \(sides)-sided dice"
    }
}

let d2 = Dice(sides: 12, generator: LinearCongruentialGenerator())
print(d2.textualDescription)
```
#### 协议类型的集合
协议类型可以在数组或者字典这样的集合中使用，在协议类型提到了这样的用法。下面的例子创建了一个元素类型为 TextRepresentable 的数组： 
``` bash
let things: [TextRepresentable] = [d, d]
for thing in things {
    print(thing.textualDescription)
}
```
#### 协议的继承
协议能够继承一个或多个其他协议，可以在继承的协议的基础上增加新的要求。协议的继承语法与类的继承相似，多个被继承的协议间用逗号分隔： 
``` bash
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {

}
```
#### 类类型专属协议
你可以在协议的继承列表中，通过添加 class 关键字来限制协议只能被类类型遵循，而结构体或枚举不能遵循该协议。class 关键字必须第一个出现在协议的继承列表中，在其他继承的协议之前： 
``` bash
protocol SomeClassOnlyProtocol: class, SomeInheritedProtocol {

}
```

#### 协议合成
有时候需要同时遵循多个协议，你可以将多个协议采用 SomeProtocol & AnotherProtocol 这样的格式进行组合，称为 协议合成（protocol composition）。你可以罗列任意多个你想要遵循的协议，以与符号(&)分隔。 
``` bash
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(to celebrator: Named & Aged) {
    print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(to: birthdayPerson)
```
#### 可选协议要求
协议可以定义可选要求，遵循协议的类型可以选择是否实现这些要求。在协议中使用 optional 关键字作为前缀来定义可选要求。可选要求用在你需要和 Objective-C 打交道的代码中。协议和可选要求都必须带上@objc属性。标记 @objc 特性的协议只能被继承自 Objective-C 类的类或者 @objc 类遵循，其他类以及结构体和枚举均不能遵循这种协议。 

#### 协议扩展
协议可以通过扩展来为遵循协议的类型提供属性、方法以及下标的实现。通过这种方式，你可以基于协议本身来实现这些功能，而无需在每个遵循协议的类型中都重复同样的实现，也无需使用全局函数。 
``` bash
extension RandomNumberGenerator {
    func randomBool() -> Bool {
        return random() > 0.5
    }
}
```
通过协议扩展，所有遵循协议的类型，都能自动获得这个扩展所增加的方法实现，无需任何额外修改： 









