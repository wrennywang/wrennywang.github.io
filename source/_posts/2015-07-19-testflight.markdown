---
layout: post
title: "TestFlight"
date: 2015-07-19 01:53:14 +0800
comments: true
categories: 
---
友盟统计的错误率一度降低到0.5%左右，但问题是否修正在个人测试或者测试部的有限测试中无法确定；这种情况下，考虑启用TestFilght Beta测试。  

**参与测试者**

1. 在iOS设备上安装TestFlight,绑定Apple ID。
2. 在iOS设备上登录邮箱，接收邀请；点击相应链接即可跳转至TestFilght中打开测试App。
>在安装时可能提示"TestFlight is currently unavailable,Try again later"。

**发布者**

1. 按平时提交App Store的步骤上传要发布的包即可。
>参与测试者出现"TestFlight is currently unavailable"提示时，重新下载distribution profile并重新打包。  
XC****** profile不可以，需要在Member Center添加Distribution Provisioning Profile。  
Build Phases> Code Signing> Provisioning Profile。
2. 至少一个Tester，打开开关才有效。
3. 外部测试需要提交Beta App审核，时间一般是一天。