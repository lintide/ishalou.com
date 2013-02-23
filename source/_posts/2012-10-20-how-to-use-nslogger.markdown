---
layout: post
title: "用NSLogger代替NSLog输出调试信息"
date: 2012-10-20 22:03
comments: true
categories: 
---

## 安装 ##

NSLogger分为两部分，`LoggerClient`和`NSLogger Viewer`，你的App需要导入前者，后者是一个独立的mac应用，NSLogger所有的调试信息将输出到这个应用中。

安装`NSLogger`:

    $ vim Podfile
    pod 'NSLogger', '1.1'

    $ pod install

如果你不了解Pod，可以参考[这里](/blog/2012/10/15/how-to-use-cocoapods/)

## 编译NSLogger Viewer ##

我在第一次编译时，系统出现了这个错误信息:

    Code Sign error: The identity '3rd Party Mac Developer Application' doesn't match any valid, non-expired certificate/private key pair in your keychains

只需将`Build Settings` => `Code Signing Identity` 设置为`Don't Code Sign`。

编译通行过后会在项目的`Products`生成一个`NSLogger.app`的文件，只须把该文件拷贝到应用目录即可。

## 使用NSLogger ##

在需要用到NSLogger的程序导入`LoggerClient.h`头文件，一般可在`ProjectName_Prefix.pch`文件导入:
    
    #import "LoggerClient.h"

现在可以用`LogMessage`等函数来代替`NSLog`了，更简单的方法是编写一个宏:

    #define NSLog(...) LogMessageF( \
            __FILE__,           \
            __LINE__,           \
            __FUNCTION__,       \
            nil, 0,             \
            __VA_ARGS__)

这样，你所有使用`NSLog`的地方将自动被`LogMessageF`取代。
