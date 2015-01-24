---
layout: post
title: "Octopress bundle fail"
date: 2015-01-24 21:36:37 +0800
comments: true
categories: 
---
####Octopress bundle fail
#####1. gem install RedCloth -v ‘4.2.9’ fail
确认ruby版本问题，似乎需要使用dev版本，目前使用2.3.0dev
#####2. 安装redcloth后必须rbenv rehash，然后再bundle install

