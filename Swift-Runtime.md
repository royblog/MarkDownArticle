---
title: Swift Runtime
date: 2017-04-20 15:31:39
tags: [iOS, Swift, Runtime]
categories: Swift
---
注：这篇文章原出处来自淘宝技术团队
***

Objective-C具有动态性，能够通过'runtime API'调用和替换任意方法，那么Swift也具有这些动态性吗？ 
### 用例分析
我们拿一个纯Swift类和一个继承自NSObject的类来做分析，这两个类里包含尽量多的Swift的类型比如Character、String、AnyObject、Tuple。

``` bash
class TestSwiftClass {
    var aBool: Bool = true
    var aInt: Int = 0
    var aFloat: Float = 12.45
    var aDouble: Double = 12.2342
    var aString: String = "abc"
    var aObject: AnyObject! = nil

    func testReturnVoidWithaId(aId: UIView) {

    }
}

class TestSwiftVC: UIViewController {
    var aBool: Bool = true
    var aInt: Int = 0
    var aFloat: Float = 12.45
    var aDouble: Double = 12.2342
    var aString: String = "abc"
    var aObject: AnyObject! = nil

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
    }

    func testReturnVoidWithaId(aId: UIView) {

    }

    func testRetrunVoidWithBool(aBool: Bool, aInteger: Int, aFloat: Float, aDouble: Double, aString: String, aObject: AnyObject) {

    }

    func testReturnTuple(aBool: Bool, aInteger: Int, aFloat: Float) -> (Bool, Int, Float) {
        return (aBool, aInteger, aFloat)
    }

    func testReturnVoidWithCharacter(aCharacter: Character) {

    }

    func tableView(tableView: UITableView, numberOfRowsSection: Int) -> Int {
        return 20
    }
}
```

### 方法、属性
动态性比较重要的一点就是能够拿到某个类所有的方法、属性，我们使用如下代码来打印方法和属性列表。
``` bash
func showClsRuntime(cls: AnyClass) {
    print("start methodList")
    var methodNum: UInt32 = 0
    let methodList = class_copyMethodList(cls, &methodNum)
    for index in 0..<numericCast(methodNum) {
        let method: Method = methodList![index]!
        print(String(_sel: method_getName(method)))
    }
    print("end methodList")

    print("start propertyList")
    var propertyNum: UInt32 = 0
    let propertyList = class_copyPropertyList(cls, &propertyNum)
    for index in 0..<numericCast(propertyNum) {
        let property: objc_property_t = propertyList![index]!
        print(String.init(utf8String: property_getName(property)) ?? "")
    }
    print("end propertyList")
}

let aSwifterClass : TestSwiftClass = TestSwiftClass()
showClsRuntime(cls: object_getClass(aSwifterClass))

print("\n")
let aSwiftVC: TestSwiftVC = TestSwiftVC()
showClsRuntime(cls: object_getClass(aSwiftVC))
```
执行上面代码结果：
``` bash
start methodList
end methodList

start propertyList
end propertyList

start methodList
.cxx_destruct
initWithNibName:bundle:
viewDidAppear:
viewDidLoad
initWithCoder:
aBool
setABool:
aInt
setAInt:
aFloat
setAFloat:
aDouble
setADouble:
aString
setAString:
aObject
setAObject:
testReturnVoidWithaIdWithAId:
testRetrunVoidWithBoolWithABool:aInteger:aFloat:aDouble:aString:aObject:
tableViewWithTableView:numberOfRowsSection:
end methodList

start propertyList
aBool
aInt
aFloat
aDouble
aString
aObject
end propertyList
```
结论：
* 对于纯Swift的TestSwiftClass来说任何方法、属性都未获取到
* 对于TestSwiftVC，除了testReturnTuple，testReturnVoidWithCharacter两个方法外，其它都获得成功。
原因：
> 纯Swift类的函数调用已经不再是Objective-c的运行时发消息，而是类似C++的vtable，在编译时就确定了调用哪个函数，所以没法通过runtime获取方法、属性。 
> TestSwiftVC继承自UIViewController，基类为NSObject，而Swift为了兼容Objective-C，凡是继承自NSObject的类都会保留其动态性，所以我们能通过runtime拿到他的方法。
> testReturnTuple，testReturnVoidWithCharacter获取不到是因为：从Objective-c的runtime 特性可以知道，所有运行时方法都依赖TypeEncoding，而Character和Tuple是Swift特有的，无法映射到OC的类型，更无法用OC的typeEncoding表示，也就没法通过runtime获取了。

### Method Swizzing
动态性最常用的就是方法替换（Method Swizzling），将类的某个方法替换成自定义的方法，从而达到hook的作用。 
* 对于纯Swift类（如TestSwiftClass）来说，无法通过objc runtime替换方法，因为由上面的测试可知拿不到这些方法、属性 
* 对于继承自NSObject类（如TestSwiftVC）来说，无法通过runtime获取到的方法肯定没法替换了。那能通过runtime获取到的方法就都能被替换吗？我们测一把。
我们替换两个可以被runtime获取到的方法：viewDidAppear和testReturnVoidWithaId 
替换代码：
``` bash 
func methodSwizze(cls: AnyClass, originalSelector: Selector, swizzledSelector: Selector) {
    let originalMethod = class_getInstanceMethod(cls, originalSelector)
    let swizzledMethod = class_getInstanceMethod(cls, swizzledSelector)

    let didAddMethod = class_addMethod(cls, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod))

    if didAddMethod {
        class_replaceMethod(cls, swizzledSelector ,method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod))
    } else {
        method_exchangeImplementations(originalMethod, swizzledMethod)
    }
}
```
viewDidAppear可以替换掉，testReturnVoidWithaId不能替换，通过代码调试，可以看到viewDidAppear会有@objc标识，testReturnVoidWithaId没有标识。
@objc用来做什么的？与动态性有关吗？ 
@objc是用来将Swift的API导出给Objective-C和Objective-C runtime使用的，如果你的类继承自Objective-c的类（如NSObject）将会自动被编译器插入@objc标识。
我们在把TestSwiftClass（纯Swift类）的方法、属性前都加个@objc 试试，如图：
``` bash 
class TestSwiftClass {
    @objc var aBool: Bool = true
    @objc var aInt: Int = 0
    @objc var aFloat: Float = 12.45
    @objc var aDouble: Double = 12.2342
    @objc var aString: String = "abc"
    @objc var aObject: AnyObject! = nil

    @objc func testReturnVoidWithaId(aId: UIView) {

    }
}
```
查看日志可以发现加了@objc的方法、属性均可以被runtime获取到了。
``` bash
start methodList
aBool
setABool:
aInt
setAInt:
aFloat
setAFloat:
aDouble
setADouble:
aString
setAString:
aObject
setAObject:
testReturnVoidWithaIdWithAId:
end methodList

start propertyList
aBool
aInt
aFloat
aDouble
aString
aObject
end propertyList
```
加了@objc标识的方法、属性无法保证都会被运行时调用，因为Swift会做静态优化。要想完全被动态调用，必须使用dynamic修饰。
使用dynamic修饰将会隐式的加上@objc标识,这也就解释了为什么testReturnVoidWithaId无法被替换，因为写在Swift里的代码直接被编译优化成静态调用了。而viewDidAppear是继承Objective-C类获得的方法，本身就被修饰为dynamic，所以能被动态替换。
我们把TestSwiftVC方法前加上dynamic再测一把，从堆栈也可以看出，方法的调用前增加了@objc标识，testReturnVoidWithaId方法被替换成功了。 

### 总结
* 纯Swift类没有动态性，但在方法、属性前添加dynamic修饰可以获得动态性。 
* 继承自NSObject的Swift类，其继承自父类的方法具有动态性，其他自定义方法、属性需要加dynamic修饰才可以获得动态性。 
* 若方法的参数、属性类型为Swift特有、无法映射到Objective-C的类型(如Character、Tuple)，则此方法、属性无法添加dynamic修饰（会编译错误） 
* Swift类在Objective-C中会有模块前缀




