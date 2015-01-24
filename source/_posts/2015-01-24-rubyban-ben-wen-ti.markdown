---
layout: post
title: "ruby版本问题"
date: 2015-01-24 21:32:31 +0800
comments: true
categories: 
---
ruby版本问题：

    rbenv install xxx安装版本
    rbenv global(local) xxx 后
    ruby -v 依然显示system版本
先查看PATH

    env | grep PATH
    
确认是否包含~/.rbenv

    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile