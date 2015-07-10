---
layout: post
title: "NSNotification相关"
date: 2015-07-10 13:34:32 +0800
comments: true
categories: 
---
* addObserver时，@selector中方法的拼写:  

```c
// 1. 字符匹配，大小写
-(void)viewDidAppear
{
    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(resignActive:) name:@"RESIGNACTIVE" object:nil];
}
-(void)resignactive:(id)sender
{
}

// 2. 参数
-(void)viewDidAppear
{
    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(resignActive::) name:@"RESIGNACTIVE" object:nil];
}
-(void)resignActive:(id)sender
{
}
```

* addObserver与removeObserver  
一般情况下在在viewDidAppear中add observer,在viewDidDisappear时removeObserver;
>注意，通过手势操作viewController可以让disappear调用无数次，而didDisappear一次也不触发,从屏幕左边缘往右滑会有个pop的效果，但是不要滑到触发pop