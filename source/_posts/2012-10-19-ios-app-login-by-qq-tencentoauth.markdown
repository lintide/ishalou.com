---
layout: post
title: "iOS应用使用QQ、新浪微博为第三方登录"
date: 2012-10-19 01:49
comments: true
categories: 
---

QQ登录SDK最重要的两个类分别为:`TencentOAuth`和`TencentRequest`，前者用于向服务器请求登录，后者用于包含请求信息。目前QQ登录在移动应用还没有采用SSO方式，未来应该会跟进。

在`ControllerView`类中声明一个类型为`TencentOAuth`的实例变量，并在`- (void)viewDidLoad`方法中初始化：

    _tencentOAuth = [[TencentOAuth alloc] initWithAppId: @"your app id" andDelegate: self];

`TencentOAuth`接受的Delegate为`TencentSessionDelegate`类型。所以`ControllerView`对象必须实现`TencentSessionDelegate`委托。该委托声明了如下方法:

    - (void)tencentDidLogin;
    // 登录成功

    - (void)tencentDidLogout;
    // 已退出

    - (void)tencentDidNotLogin: (BOOL);
    // 登录失败，一种为用户单击了取消按钮。

    - (void)tencentDidNotNetwork;
    // 没有网络

实现上面的所有方法，接下来向服务器发出认证请求

    [_tencentOAuth authorize:_permissions inSafari:NO];

`_permissions`是一个数组，数组的每一项代表用户授权的OpenAPI列表，例如可以发表微博，发表说说等。`inSafari`是否需要在safari中进行登录，默认为NO;

## Weibo登录 ##

新浪微博已经采用了SSO登录模式，SSO即`Single Sign On`，单点登录。在官方库中，`SinaWeibo`和`SinaWeiboRequest`是两个最重的类，首先声明一个`SinaWeibo`实例变量:

    SinaWeibo *weiboOAth;

创建`SinaWeibo`实例：

    weiboOAuth = [[SinaWeibo alloc] initWithAppKey:kAppKey appSecret:kAppSecret appRedirectURI:kAppRedirectURI andDelegate: delegate];

待续...