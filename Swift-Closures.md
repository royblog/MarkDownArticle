---
title: Swift Closures
date: 2017-04-26 15:11:50
tags: [iOS, Swift, Closures]
categories: Swift
---
> **闭包是自包含的函数代码块**，可以在代码中被传递和使用。Swift 中的闭包与 C 和 Objective-C 中的代码块（blocks）以及其他一些编程语言中的匿名函数比较相似。 
> **闭包可以捕获和存储其所在上下文中任意常量和变量的引用**。被称为包裹常量和变量。 Swift 会为你管理在捕获过程中涉及到的所有内存操作。 

 闭包包含三种形式：
* 全局函数是一个有名字但不会捕获任何值的闭包 
* 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包 
* 闭包表达式是一个利用轻量级语法所写的可以捕获其上下文中变量或常量值的匿名闭包 

Swift 的闭包表达式拥有简洁的风格，并鼓励在常见场景中进行语法优化，主要优化如下： 
* 利用上下文推断参数和返回值类型 
* 隐式返回单表达式闭包，即单表达式闭包可以省略 return 关键字 
* 参数名称缩写 
* 尾随闭包语法 

### 闭包表达式
闭包表达式是一种利用简洁语法构建内联闭包的方式。闭包表达式提供了一些语法优化，使得撰写闭包变得简单明了。下面闭包表达式的例子通过使用几次迭代展示了 sorted(by:) 方法定义和语法优化的方式。每一次迭代都用更简洁的方式描述了相同的功能。
##### sorted方法
Swift 标准库提供了名为 sorted(by:) 的方法，它会根据你所提供的用于排序的闭包函数将已知类型数组中的值进行排序。一旦排序完成，sorted(by:) 方法会返回一个与原数组大小相同，包含同类型元素且元素已正确排序的新数组。原数组不会被 sorted(by:) 方法修改。 
使用sorted(by:)对下面数组排序：
``` bash
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"] 
```
根据规则：全局函数是一个有名字但不会捕获任何值的闭包 ，我们创建一个普通的函数backward()，sorted(by:)接受一个闭包，该闭包函数需要传入与数组元素类型相同的两个值，并返回一个布尔类型值来表明当排序结束后传入的第一个参数排在第二个参数前面还是后面。如果第一个参数值出现在第二个参数值前面，排序闭包函数需要返回true，反之返回false。我们根据规则生成了一个如下的闭包函数：
``` bash
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
```
排序代码：
``` bash
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reverseNames = names.sorted(by: backward)
```
#### 闭包表达式语法
可以使用闭包表达式对上述代码进行优化。
闭包表达式的语法一般形似：
``` bash
{ (parameters) -> returnType in
    statements
}
```
闭包的函数体部分由关键字in引入。该关键字表示闭包的参数和返回值类型定义已经完成，闭包函数体即将开始。 

我们可以对backward(_:_:)转化为闭包表达式：
``` bash
var reverseNames = names.sorted(by: {
    (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```
#### 根据上下文推断类型
因为排序闭包函数是作为 sorted(by:) 方法的参数传入的，Swift 可以推断其参数和返回值的类型。sorted(by:) 方法被一个字符串数组调用，因此其参数必须是 (String, String) -> Bool 类型的函数。这意味着 (String, String) 和 Bool 类型并不需要作为闭包表达式定义的一部分。因为所有的类型都可以被正确推断，返回箭头（->）和围绕在参数周围的括号也可以被省略： 
``` bash
var reverseNames = names.sorted(by: {
    s1, s2 in return s1 > s2
})
```
#### 单行表达式闭包隐式返回
在这个例子中，sorted(by:) 方法的参数类型明确了闭包必须返回一个 Bool 类型值。因为闭包函数体只包含了一个单一表达式（s1 > s2），该表达式返回 Bool 类型值，因此这里没有歧义，return 关键字可以省略。 
``` bash
var reverseNames = names.sorted(by: {
    s1, s2 in s1 > s2
})
```
#### 参数名称缩写
Swift 自动为内联闭包提供了参数名称缩写功能，你可以直接通过 $0，$1，$2 来顺序调用闭包的参数，以此类推。 
如果你在闭包表达式中使用参数名称缩写，你可以在闭包定义中省略参数列表，并且对应参数名称缩写的类型会通过函数类型进行推断。in关键字也同样可以被省略，因为此时闭包表达式完全由闭包函数体构成： 
``` bash
var reverseNames = names.sorted(by: {
    $0 > $1
})
```
#### 运算符方法 
实际上还有一种更简短的方式来编写上面例子中的闭包表达式。Swift 的 String 类型定义了关于大于号（>）的字符串实现，其作为一个函数接受两个 String 类型的参数并返回 Bool 类型的值。而这正好与 sorted(by:) 方法的参数需要的函数类型相符合。因此，你可以简单地传递一个大于号，Swift 可以自动推断出你想使用大于号的字符串函数实现： 
``` bash
var reverseNames = names.sorted(by: > )
```
#### 尾随闭包
如果你需要将一个很长的闭包表达式作为最后一个参数传递给函数，可以使用尾随闭包来增强函数的可读性。尾随闭包是一个书写在函数括号之后的闭包表达式，函数支持将其作为最后一个参数调用。在使用尾随闭包时，你不用写出它的参数标签： 
``` bash
func someFuncationThatTakesAClosure(closure: () -> Void) {
    
}

//不使用尾随闭包
someFuncationThatTakesAClosure(closure: {
    
})

//使用尾随闭包
someFuncationThatTakesAClosure { 

}
```
在sorted(by:)方法中，可以简化为：
``` bash
reversedNames = names.sorted() { $0 > $1 }
```
如果闭包表达式是函数或方法的唯一参数，则当你使用尾随闭包时，你甚至可以把 () 省略掉： 
``` bash
reversedNames = names.sorted{ $0 > $1 }
```
当闭包非常长以至于不能在一行中进行书写时，尾随闭包变得非常有用。举例来说，Swift 的 Array 类型有一个 map(_:) 方法，这个方法获取一个闭包表达式作为其唯一参数。该闭包函数会为数组中的每一个元素调用一次，并返回该元素所映射的值。具体的映射方式和返回值类型由闭包来指定。 
下例介绍了如何在 map(_:) 方法中使用尾随闭包将 Int 类型数组 [16, 58, 510] 转换为包含对应 String 类型的值的数组["OneSix", "FiveEight", "FiveOneZero"]：
``` bash
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",  3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven",8: "Eight", 9: "Nine"
]

let numbers = [16, 58, 510]

let strings = numbers.map { (number) -> String in
    var number = number
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}

print(strings)
```
字典 digitNames 下标后跟着一个叹号（!），因为字典下标返回一个可选值（optional value），表明该键不存在时会查找失败。在上例中，由于可以确定 number % 10 总是 digitNames 字典的有效下标，因此叹号可以用于强制解包 (force-unwrap) 存储在下标的可选类型的返回值中的String类型的值。 

### 值捕获
闭包可以在其被定义的上下文中捕获常量或变量。即使定义这些常量和变量的原作用域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。 
Swift 中，可以捕获值的闭包的最简单形式是嵌套函数，也就是定义在其他函数的函数体内的函数。嵌套函数可以捕获其外部函数所有的参数以及定义的常量和变量。 
``` bash
func makeIncrementer(forIncrement amout: Int) -> () -> Int {
    var runningTotal = 3
    func incrementer() -> Int {
        runningTotal += amout
        return runningTotal
    }
    return incrementer
}

var a = makeIncrementer(forIncrement: 5)()
```
定义一个函数makeIncrementer，返回值为() -> Int，意味着返回值是一个函数，其中嵌套一个函数incrementer。incrementer() 从上下文中捕获了两个值，runningTotal 和 amount。捕获这些值之后，makeIncrementer 将 incrementer 作为闭包返回。每次调用 incrementer 时，其会以 amount 作为增量增加 runningTotal 的值。
incrementer() 函数并没有任何参数，但是在函数体内访问了 runningTotal 和 amount 变量。这是因为它从外围函数捕获了 runningTotal 和 amount 变量的引用。捕获引用保证了 runningTotal 和 amount 变量在调用完 makeIncrementer 后不会消失，并且保证了在下一次执行 incrementer 函数时，runningTotal 依旧存在。 

### 闭包是引用类型
上面的例子中，incrementBySeven 和 incrementByTen 都是常量，但是这些常量指向的闭包仍然可以增加其捕获的变量的值。这是因为函数和闭包都是引用类型。 
无论你将函数或闭包赋值给一个常量还是变量，你实际上都是将常量或变量的值设置为对应函数或闭包的引用。上面的例子中，指向闭包的引用 incrementByTen 是一个常量，而并非闭包内容本身。 

### 逃逸闭包



















