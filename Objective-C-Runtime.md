---
title: Objective-C Runtime
date: 2017-04-21 09:17:10
tags: [iOS, OC, Runtime]
categories: OC
---
Objective-C Runtime是一个运行时库（Rumtime Library）,可以理解为一个运行时系统。主要使用C语言和汇编语言编写。
### Objective-C如何和Runtime打交道？
* 通过Objective-C源代码（Objective-C Source Code）
* 通过Foundation框架中的NSObject定义的方法（NSObject Methods）
* 通过直接调用runtime函数(Runtime Funcations)

### 消息机制
在大部分情况下，运行时系统（Runtime System）能够在幕后自动处理相关任务，我们仅仅需要写Objective-C代码就可以。
当我们编译包含Objective-C类和方法的代码时，编译器创建数据结构和函数调用实现语言的动态性特征。其中比较重要的一个运行时机制是`消息机制`。

消息机制的大致流程为：
首先，Runtime系统将方法调用转化为消息发送，会把
``` bash
[receive message];
```
转化为一个消息函数objc_msgSend：
``` bash
objc_msgSend(receiver, selector)
```
如果含有参数，则为：
``` bash
objc_msgSend(receiver, selector, arg1, arg2, ...)
```
此时方法调用会通过isa指针找到所属的类，然后检查是否存在相关方法，会先检查缓存中是否有方法（为了提高性能，runtime system 会缓存使用到的selector和方法地址），找到就直接调用方法。如果没有找到，会通过super_class查找父类，如果知道NSObject都没有找到，也没有做任何处理，程序会崩溃，为了防止程序崩溃，可以做消息转发。
![消息机制](http://roywyg.oss-cn-shanghai.aliyuncs.com/Image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-04-21%20%E4%B8%8A%E5%8D%8811.44.20.png)

上面提到了isa指针，isa是什么呢？
OC中大部分对象都继承NSObject,NSObject定义：
``` bash
@interface NSObject <NSObject> {
    Class isa  OBJC_ISA_AVAILABILITY;
}
```
``` bash
typedef struct objc_class *Class;
```
``` bash
struct objc_object {
    Class isa  OBJC_ISA_AVAILABILITY;
};
```
``` bash
typedef struct objc_object *id;
```
我们可以看出Class是指向objc_class的一个结构体指针，而id是一个指向objc_object结构体的指针，其中的isa是一个指向objc_class结构体的指针。其中的id就是我们所说的对象，Class就是我们所说的类。
objc_class定义如下：
``` bash
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

    #if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
    #endif

} OBJC2_UNAVAILABLE;
```


### 消息转发
发送的消息没有被处理，程序就会出错，然而在出错前，运行时机制能给接受对象一个机会处理消息。
在错误之前，runtime会发送消息给forwardInvocation：，我们实现forwardInvocation：方法，给消息一个默认的响应即可。

### Runtime函数
在使用OC编程的时候，大部分不需要直接写Runtime，因为在使用OC的时候，很多内部转化成了Runtime。使用Runtime函数有时能实现某些特殊的效果，下面是runtime常用的函数。
[runtime常用函数](https://developer.apple.com/reference/objectivec/objective_c_runtime)

### 总结
Runtime，在应用中使用广泛，例如消息传递（系统已经帮我们实现），解决字典模型转换问题，快速归档，给对象动态添加变量，动态的添加方法，访问私有变量等。


















