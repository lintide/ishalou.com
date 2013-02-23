---
layout: post
title: "expressjs及mongoose入门"
date: 2012-10-26 12:04
comments: true
categories: 
---

## expressjs 调试 ##
如果使用

    $ node app.js

的方式运行服务，每次更改服务器代码时都需要手动结果程序然后再运行，显然在开发阶段更改代码是很频繁的。使用`supervisor`帮忙省下这些麻烦，一但文件更改，`supervisor`将自动加载。

    $ sudo npm install supervisor -g
    $ supervisor app.js

