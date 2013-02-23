---
layout: post
title: "cocos2d v2.0源码分析"
date: 2013-02-23 14:15
comments: true
categories: 
---
## Cocos2d中的关键类及继承关系(以下讨论只涉及iOS版本，不考虑mac版) ##

`CCDirector`继承自`UIViewController`类

`CCNode`继承自`NSObject`

`CCLayer`继承自`CCNode`

`CCScene`继承自`CCNode`
