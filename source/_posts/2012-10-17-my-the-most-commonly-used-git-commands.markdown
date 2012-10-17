---
layout: post
title: "我最常用的git命令"
date: 2012-10-17 12:43
comments: true
categories: 
---
## 文件管理 ##

添加新增文件或已修改文件到暂存

    $ git add filename

有多个文件更改时

    $ git add .

提交更新
    
    $ git commit -m 'msseage'


## 库管理  ##

在本地初始化一个库

    $ cd dir
    $ git init

从远程克隆一个库

    $ git clone git://github.com/lintide/jekyll

上面克隆的库是只读的，如果你对该库拥用写权限，可以使用`git remote set-url`修改上面的远程地址

    $ git remote -v
    origin git://github.com/lintide/jekyll

    $ git remote set-url origin git@github.com:username/jekyll

`username`是你在`github.com`的用户名，`git@github.com`表示采用`ssh`登录github.com，这样你就不需要每次都输入用户名了。关于`ssh`登录的配置方法可以参考其它文章。

打标签
    
    $ git tag -a v1.0.0 -m "version 1.0.0"

显示所有标签

    $ git tag

提交标签不是使用`git commit`命令，而是

    $ git push --tags

> 边学边用边记，持续更新中...