---
title: Swift Optional
date: 2017-04-20 09:48:15
tags: [iOS, Swift, Optional]
categories: Swift
---

### Optional定义
Optional是在Swift中引入的新类型，特点是可以有值，也没有没有值，没有值为nil。Optional其实就是一个枚举：

``` bash
public enum Optional<Wrapped> : ExpressibleByNilLiteral {

    case none
    case some(Wrapped)

    public init(_ some: Wrapped)
    public func map<U>(_ transform: (Wrapped) throws -> U) rethrows -> U?
    public func flatMap<U>(_ transform: (Wrapped) throws -> U?) rethrows -> U?
    public init(nilLiteral: ())
    public var unsafelyUnwrapped: Wrapped { get }
}
```

当Optional没有值时，返回的nil其实就是Optional.None，即没有值。除了None以外，还有一个Some，当有值时就是被Some<T>包装的真正的值，所以我们拆包的动作其实就是将Some里面的值取出来。

### Optional使用

我们可以声明一个 Optional 的 String 类型的变量，只需要在变量定义的时候在类型后面加上一个 ?

``` bash
var optionalString: String?
```
在引用的时候，我们需要强制拆包，在变量后面添加一个 ! ，相当于告诉编译器，我确信这个变量不是 nil，可以直接使用（当然，使用强制解包只代表你自己确认它不为 nil，但它还是有可能为 nil 的，如果这样的情况发生，依然会造成程序运行时崩溃）。
``` bash
print(optionalString!)
```
使用 if let 这样的语法就可以更加安全的操作 Optional 值。只有在 name 中的值不为 nil 的时候，nameValue 变量才会被初始化成功。 这样我们的 print 语句就不会因为 nil 而崩溃。相比使用强制解包，更加安全和优雅的方式是使用 Optional Chaining：
``` bash
if let newValue = optionalString {
    print(newValue)
}
```

### Optional Chaning 陷阱
``` bash
struct Name {
    var firstName: String = ""
    var lastName: String = ""
}

struct Person {
    var name: Name?
    var age: Int
}

var person: Person? = Person(name: Name(firstName: "roy", lastName: "wang"), age: 25)
print(person?.name?.firstName)
```
上面的输入结果是：
``` bash
Optional("roy")
```
firstName不是可选类型，为什么输出结果是这样呢。因为在表达式中只要有一个是可选值，整个表达式结果都是Optional。
如果person为nil，后面的引用就没有了意义，所以我们需要对这个表达式做处理。正确的引用方式应该是这样的：
``` bash
if let firstName = person?.name?.firstName {
    print(firstName)
}
```
Optional Chaning 作为返回值也需要注意，如下：
``` bash
func getName(person: Person) -> String {
    return person.name?.firstName
}
```
上面的代码不会通过编辑，因为返回值是Optional Chaning，所以下面的才是正确的打开方式：
``` bash
func getName(person: Person) -> String? {
    return person.name?.firstName
}
```

### 实际应用
``` bash
class WebViewDelegate :NSObject, UIWebViewDelegate {
    func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {
        if let absURL = request.URL?.absoluteString {
            // do something..
            return false
        }
        return true
    }
}
```
不要天真的这样使用,编译会出错的：
``` bash
if request.URL?.absoluteString == "xxx" {
}
```
### 为什么要有Optional
因为Swift是强类型语言。引入 Optional 机制是为了「更严格的」类型检查，使得在编译时可以发现更多问题（未预期的变量为空）。




