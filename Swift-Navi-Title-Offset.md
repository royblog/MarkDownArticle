---
title: Swift Navi Title Offset
date: 2017-04-18 13:06:39
tags: [iOS, OC, Navigation]
categories: OC
---
在iOS中，导航栏的标题有时不居中显示，问题的原因是前一个导航页面标题太长，进入下一个界面的时候，下个界面的标题会向后偏移，导致标题不居中显示。
问题的解决方案有多种，在此说一下我的解决方案，因为项目中有基类控制器，在基类控制器的viewDidLoad方法中，添加以下代码：
``` bash
NSArray *viewControllerArray = [self.navigationController viewControllers];
long previousViewControllerIndex = [viewControllerArray indexOfObject:self] - 1;
UIViewController *previous;
if (previousViewControllerIndex >= 0) {
previous = [viewControllerArray objectAtIndex:previousViewControllerIndex];
previous.navigationItem.backBarButtonItem = [[UIBarButtonItem alloc]
initWithTitle:@""
style:UIBarButtonItemStylePlain
target:self
action:nil];
}
```
