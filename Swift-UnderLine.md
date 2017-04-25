---
title: Swift Underline
date: 2017-04-17 11:20:46
tags: [Swift, Underline, iOS]
categories: Swift

---
在Swift中下划线用途多样，下面列举几种常见用法。
## 1.格式化数字字面量
提高数字字面量的可读性。
``` bash
let oneMillion = 1_000_000 //(is equal to 1,000,000)
```

## 2.忽略元组元素值
使用元组时，有元素不必使用时，可以使用下划线将元素忽略。
``` bash
let http404Error = (404, "Not Found")
let(_, errorMessage) = http404Error
```

## 3.忽略区间值
``` bash
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
   answer *= base
}
```

## 4.忽略外部参数名
``` bash
func cal(num1: Int, num2: Int) -> Int {
    return num1 + num2
}
cal(num1: 15, num2: 15)

func sum(_ num1: Int, _ num2: Int) -> Int {
    return num1 + num2
}
sum(15, 15)
```

参考：[@Author:twlkyao](http://blog.csdn.net/twlkyao)

