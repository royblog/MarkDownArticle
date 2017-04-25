---
title: Swift Define
date: 2017-04-18 17:19:37
tags: [iOS, Swift, Define]
categories: Swift
---
宏定义合理使用能够使代码漂亮简洁，但是使用宏定义有时也会导致难以重构和维护，隐藏很多问题。
在Swift中已经去掉了宏定义，并且苹果公司给了一些替代建议，比如使用let和get代替原来的宏定义。

### 没有参数的宏
在OC中

``` bash
#define kScreenHeight     [UIScreen mainScreen].bounds.size.height
#define kScreenWidth      [UIScreen mainScreen].bounds.size.width
```

在Swift中

``` bash
let kScreenHeight = UIScreen.mainScreen().bounds.size.height
let kScreenWidth = UIScreen.mainScreen().bounds.size.width
```

### 接收参数的宏

在OC中

``` bash
#define RGBCOLOR(r,g,b) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:1]
```
在Swift中

``` bash
func RGBCOLOR(_ r:CGFloat,_ g:CGFloat,_ b:CGFloat) -> UIColor
{
    return UIColor(red: (r)/255.0, green: (g)/255.0, blue: 
}
```


