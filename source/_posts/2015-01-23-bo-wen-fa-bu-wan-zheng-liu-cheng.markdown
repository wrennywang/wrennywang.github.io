---
layout: post
title: "博文发布完整流程"
date: 2015-01-23 23:13:33 +0800
comments: true
categories: 
---
rake new_post["New Post"]
#edit the file source/_posts/{DATETIME}-New-Post.markdown
rake generate
git add .
git commit -am "Some comment here." 
git push origin source
rake deploy