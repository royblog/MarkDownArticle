---
title: Swift 存储属性、类属性、计算属性
date: 2017-04-25 16:26:10
tags: [iOS, Swift, property]
categories: Swift
---
存储型属性，主要用来存储值，使用实例访问
类属性使用对象直接访问，使用static修饰
计算型属性，不能直接存储值，使用get/set来取值和赋值，可以操作其它属性的变化

``` bash
class Student {
    
    //定义（存储）属性
    var name: String?
    var age: Int = 0
    var gender: String?
    
    //定义类属性
    static var skin: String?
    
    //计算属性（get）
    var doubleAge: Int {
        return age * 2
    }
    
    var fiveTime: Int {
        get {
            return age * 5
        }
        
        set(newValue) {
            age = newValue + 1
        }
    }
}

let stu = Student()

stu.name = "roy"
if let name = stu.name {
    print(name)
}
stu.age = 13
stu.gender = "boy"
stu.doubleAge
stu.fiveTime = 23
stu.fiveTime
```




