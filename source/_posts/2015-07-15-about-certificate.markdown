---
layout: post
title: "About Certificate"
date: 2015-07-15 13:12:16 +0800
comments: true
categories: 
---
  今天需要改个版本给深圳同事进行测试，exporting ipa时提示"Your account already has a valid iOS distribution certificate";  
  看来需要重新整一遍证书，添加，已有两个Distribution证书，上午没弄利索前Production中App Store and Ad Hoc还是可选，导入根证书后生成，最后一直loading；网上找了一些资料，才知道最多两个发布证书；  
  那就注销一个吧，点revoke后没反应...再找资料，有同学说跟苹果客服联系过，使用的chrome,需要换safari;换safari后，网页打开有点慢，但顺利注销，添加；  
  三月份的时候第一次发布的时候整过一次，当时就是因为用safari添加证书一直不成功菜换的chrome，现在反过来了，几个意思？难道因为OS X升级了？另外，现在再进行证书添加操作App Store and Ad Hoc选项变灰已不可选，上午难道是幻觉？  
