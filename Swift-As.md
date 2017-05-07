---
title: Swift As
date: 2017-05-02 09:35:21
tags: [iOS, Swfit, As]
categories: Swift
---

#### 1.从派生类转化为基类，向上转型
```  bash
class Animal {
    var a = "ani"
}

class Cat: Animal {
    var c = "cat"
}

let cat = Cat()
let animal = cat as Animal
```

#### 2.消除二意性，数值类型转化
``` bash
let num1 = 25 as Int
let num2 = 25 as Float
let num3 = 25 as Double
print(num1,num2,num3)
```

#### 3.switch语句进行模式匹配
``` bash
class Animal {
    
}

class Cat: Animal {
    
}

class Dog: Animal {
    
}

let cat = Cat()
let animal = cat as Animal


switch animal {
case let cat as Dog:
    print("ani object")
case let cat as Cat:
    print("cat object")
default:
    break
}
```
#### 4.0 as! 向下转型使用，强制转换类型，转换失败，会曝错误
``` bash
let animal: Animal = Cat()
let cat = animal as! Cat
```

#### 5.0 as? 转化成功返回可选值类型，需要拆包使用，转化失败，返回nil
由于as?在转换失败也不会报错，如不能百分百确保转换成功，使用as?，能确定成功，可以使用as!
``` bash
let animal: Animal = Cat()
if let cat = animal as? Cat {
    print("cat is not nil")
} else {
    print("cat is nil")
}
```

