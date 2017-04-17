---
title: Swift Generic Base
date: 2017-04-17 14:22:12
tags: [iOS, Swift, Generic]
categories: Swift
---

Swift创始人Chirs Lattner:

``` bash
Swift引入泛型和函数式编程的思想，极大地扩展了设计的空间。
```

## 什么是泛型？

### 以下面两个例子说明什么是泛型
* 两值是否相等问题
* 两值交换问题

### 两值是否相等问题
判断两值是否相等我们的常规思路如下：

``` bash
func equalInt(numOne: Int, numbTwo: Int) -> Bool {
return numOne == numbTwo
}
equalInt(numOne: 4, numbTwo: 4)

func equalString(strOne: String, strTwo: String) -> Bool {
return strOne == strTwo
}
equalString(strOne: "roy", strTwo: "note")
```

我们需要为每一种类型写一种方法，一句话总结就是写的很不爽，下面就该泛型登场了

``` bash
func isEquals<T: Comparable>(a: inout T, b: inout T) -> Bool {
return (a == b)
}
var num1 = 5
var num2 = 5
var str1 = "wyg"
var str2 = "roy"
isEquals(a: &num1, b: &num2)
isEquals(a: &str1, b: &str2)
```

并不是所有的类型都具有可比性，必须遵守Comparable协议，在Swift中，基本数据类型和字符串都遵守Comparable协议。
### 两值交换问题
有了上面例子铺垫，直接使用泛型来实现

``` bash
func swapTwoValue<T>(a: inout T, b: inout T) {
let tempValue = a
a = b
b = tempValue
}

var oneStr = "hello"
var twoStr = "world"
swapTwoValue(a: &oneStr, b: &twoStr)
print(oneStr,twoStr)
```

## 类型参数
在上面的泛型函数例子中，占位符T是类型参数的一个例子。类型参数指定并命名一个占位符类型，并用<>包裹，放在函数名后面。一旦一个参数类型确定，就可以指定参数类型，或者返回值的类型，还可以用作函数体的注释类型。在调用的时候会被实际的类型替代，如传递的是Int，就替换为Int，如果传入的是Double类型就替换为Double等等





