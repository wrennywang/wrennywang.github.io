---
layout: post
title: "CocoaPods:pod install异常"
date: 2015-01-24 00:19:46 +0800
comments: true
categories: 
---
pod install失败时，注意podfile中字符，键盘智能替换会将(')替换为(‘)
```
1. 系统偏好设置>键盘>文本>使用智能引号和破折号－－－修改单引号设置
2. 文本编辑器>编辑>替换>智能引号——去掉勾选
```