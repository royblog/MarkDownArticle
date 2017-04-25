---
title: Swift Add Variable For A Exist Class
date: 2017-04-18 16:22:02
tags: [iOS, Swift, Variable]
categories: Swift
---

在Swift中如何为一个存在的类添加变量，新建一个Person.swift文件，代码如下：
``` bash
import Foundation

class Person: NSObject {
    var name: NSString?
    func eatFood() -> Void {
        print("People can eat")
    }
}

```

现在新建PersonExtension.swift，代码如下：

``` bash
import Foundation

private var key: Void?

extension Person {
    var gender: String? {
        get {
            return objc_getAssociatedObject(self, &key) as? String
        }

        set {
            return objc_setAssociatedObject(self, &key, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
    }
}
```

现在我们则为Person新添加了一个变量,可以使用一下代码访问新变量

``` bash
let per = Person()
per.name = "roy"
per.gender = "boy"
per.eatFood()
```
