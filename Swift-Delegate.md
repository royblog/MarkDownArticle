---
title: Swift Delegate
date: 2017-04-26 14:30:33
tags: [iOS, Swift, Delegate]
categories: Swift
---
委托（代理）是一种设计模式，它允许类或结构体将一些需要它们负责的功能委托给其他类型的实例。委托模式的实现很简单：定义协议来封装那些需要被委托的功能，这样就能确保遵循协议的类型能提供这些功能。委托模式可以用来响应特定的动作，或者接收外部数据源提供的数据，而无需关心外部数据源的类型。 
***
简单模拟一下tableview的实现：
``` bash
protocol TableViewDelegate {
    func numberOfRow(row: Int)
}

class TableView {
    var delegate: TableViewDelegate?
    
    func clickRow() {
        //检查委托对象是否实现了协议方法，如实现，则调用
        delegate?.numberOfRow(row: 3)
    }
}

class MyController: TableViewDelegate {
    func numberOfRow(row: Int) {
        print("num is \(row)")
    }
}

let table = TableView()
table.delegate = MyController()
table.clickRow()
```
结果为：
``` bash
num is 3
```
换一个马甲，代理传值问题也是同理，从ConB向ConA传递值
``` bash
protocol Method {
    func delevier(Str: String)
}

class ConA: Method {
    func delevier(Str: String) {
        print("str is \(Str)")
    }
}

class ConB {
    var delegate: Method?
    func click() {
        delegate?.delevier(Str: "roy")
    }
}

let b = ConB()
b.delegate = ConA()
b.click()
```



