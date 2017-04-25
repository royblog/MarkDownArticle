---
title: Swift Class Struct Enum
date: 2017-04-24 10:50:00
tags: [iOS, Swift]
categories: Swift
---
### 相同点：
* 三者都可以拥有属性和方法（enum本身不能存储属性，但是可以存储在其关联的信息中，可以有计算属性）。
* 都可以拥有函数。
* class和struct可以拥有自己的构造器。
* 支持扩展增加功能
* 可以遵循协议

### 不同点：
* 类可以继承。
* 类可以内省和转化。
* struct和enum为值类型，class为引用类型。
* class类方法用class关键字声明，enum、struct用static关键字
* 用mutaing关键词生命要修改struct、enum内容的方法。
* 结构体适合基本数据类型。

引用类型：(reference types，通常是类)被复制的时候其实复制的是一份引用，两份引用指向同一个对象。
值类型：(value types)的每一个实例都有一份属于自己的数据，在复制时修改一个实例的数据并不影响副本的数据。

### Enum
#### 枚举语法
Swift使用enum关键字创建枚举，使用case创建每一个枚举值。和OC不同，Swift枚举不会默认创建分配值。
``` bash
enum Student {
    case pupilStudent
    case middleStudent
    case collegeStudent
}
print(Student.middleStudent)
```
``` bash
结果为：middleStudent
```
枚举值可以写在同一个case中，使用逗号分割：
``` bash
enum Teacher {
    case mathTeacher, chineseTeacher, englishTeacher
}
```
枚举经常和Switch结合使用：
``` bash
var teacher = Teacher.chineseTeacher
switch teacher {
case .mathTeacher:
    print("math teacher")
case .chineseTeacher:
    print("chinese teacher")
case .englishTeacher:
    print("english teacher")
}
```
#### 枚举原始值
原始值可以理解为为枚举设置一个具体类型：
``` bash
enum Student: String {
    case pupilStudent = "A"
    case middleStudent
    case collegeStudent
}
```
结果为：
``` bash
A
middleStudent
```
如果枚举时Int类型，则会类似于OC，枚举原始值会依次增加：
``` bash
enum Student: Int {
    case pupilStudent
    case middleStudent = 13
    case collegeStudent
}
print(Student.pupilStudent.rawValue)
print(Student.middleStudent.rawValue)
print(Student.collegeStudent.rawValue)
```
结果为：
``` bash
0
13
14
```
#### 枚举相关值（关联值）
Swift中的枚举有一个特点，可以设置一些相关值：
``` bash
enum Student {
    case pupilStudent(String)
    case middleStudent(Int, String)
    case collegeStudent(Int, String)
}
let stu = Student.collegeStudent(7, "gameing")
switch stu {
case .pupilStudent(let things):
    print("is a pupil and \(things)")
case .middleStudent(let day, let things):
    print("is a middle and \(day) days \(things)")
case .collegeStudent(let day, let things):
    print("is college and \(day) days \(things)")
}
```
结果为：
``` bash
is college and 7 days gameing
```
#### 枚举递归
递归枚举是拥有另一个枚举作为枚举成员关联值的枚举。(递归枚举值必须使用indirect关键字修饰)
``` bash
enum Expression {
    case num(Int)
    indirect case add(Expression, Expression)
    indirect case mul(Expression, Expression)
}
var exp1 = Expression.num(5)
var exp2 = Expression.num(5)
var exp3 = Expression.add(exp1, exp2)
var exp4 = Expression.mul(exp1, exp3)
```
如果所有case都是可以递归的：
``` bash
indirect enum Expression {
    case num(Int)
    case add(Expression, Expression)
    case mul(Expression, Expression)
}
```
### Struct
#### 结构体定义
结构体是由一系列具有相同类型或不同类型的数据构成的数据集合。结构体是一种值类型的数据结构，在Swift中常常使用结构体封装一些属性甚至是方法来组成新的复杂类型，目的是简化运算。我们通过使用关键词 struct 来定义结构体。并在一对大括号内定义具体内容包括他的成员和自定义的方法（是的，Swift中的结构体有方法了），定义好的结构体存在一个自动生成的成员初始化器，使用它来初始化结构体实例的成员属性。废话不多说直接上代码： 
``` bash
struct Student {
    var chinese :Int
    var math: Int
    var english: Int
}
let stu = Student(chinese: 89, math: 98, english: 84)
```
也可以赋初始值：
``` bash
struct Student {
    var chinese :Int = 60
    var math: Int = 60
    var english: Int = 60
}
let stu = Student(chinese: 89, math: 98, english: 84)
```
#### 自定义初始化器
``` bash
import Foundation
struct Student {
    var chinese :Int = 60
    var math: Int = 60
    var english: Int = 60
    init(chinese: Int, math: Int, english: Int) {
        self.chinese = chinese
        self.math = math
        self.english = english
    }
    
    init(stringScore: String) {
        let cme = stringScore.characters.split(separator: ",")
        chinese = Int(atoi(String(cme.first!)))
        math = Int(atoi(String(cme[1])))
        english = Int(atoi(String(cme.last!)))
    }
}
let stu = Student(chinese: 89, math: 98, english: 84)
let stu1 = Student(stringScore: "87,56,83")
print(stu1.chinese)
```
一旦我们自定义了初始化器，系统自动的初始化器就不起作用了，如果还需要使用到系统提供的初始化器，在我们自定义初始化器后就必须显式的定义出来。
#### 定义其它方法
如果修改某一个学生的成绩怎么处理，比如修改数学成绩为85:
``` bash
struct Student {
    var chinese :Int = 60
    var math: Int = 60
    var english: Int = 60
    
    mutating func modifyMathScore(num: Int) {
        self.math = num
    }
}
var stu = Student(chinese: 89, math: 98, english: 84)
stu.modifyMathScore(num: 85)
print(stu.math)
```
修改内部变量使用mutating修饰，如果使用使用类方法，使用static修饰函数。


