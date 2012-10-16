---
layout: post
title: "如何编写一个CocoaPods的spec文件"
date: 2012-10-16 19:04
comments: true
categories: 
---

## CocoaPods命令介绍 ##

在[上一篇文章](/blog/2012/10/15/how-to-use-cocoapods/)中，已经介绍过CocoaPods的几条基本命令。`pod setup`用于初始化本地第三方库的Spec描述文件，所有的spec文件存都存放在`~/.cocoapods`目录中。`pod install`用来安装或删除`Podfile`文件声明中的第三方依赖库。下面继续介绍其它一些命令。

    $ pod list
    # 列出所有可用的第三方库

<span></span>

    $ pod search query

搜索名称包含`query`的类库，`query`可以替换为你想搜索的名字（如`json`)，不区分大小写。也可以使用`pod search --full query`命令作更仔细的搜索，该命令不但搜索类库的名称，同时还搜索类库的描述文本，所以搜索速度也相对慢一些。

`pod list`和`pod search`命令只搜索存在于本地`~/.cocoapods`文件夹的所有第三方库，并不会连接到远程服务器。如果你要从服务器更新本地第三方库的描述文件，可以:

    $ pod repo update master

## 创建自己项目的Podspec描述文件 ##

CocoaPods还是一个相对年轻的项目，所有的项目的Podspec文件都托管在[https://github.com/CocoaPods/Specs](https://github.com/CocoaPods/Specs)。可能有一些库并未收录其中。下面我们通过为[微博sso认证登录库](https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth)编写Podspec文件来学习相关的概念。

初始化一个Podspec文件

    $ pod spec create weibo_ios_sdk_sso-oauth

该命令将在本目录产生一个名为`weibo_ios_sdk_sso-oauth.podspec`的文件。用编辑器打开该文件，里面已经有非常丰富的说明文档。下面我们介绍如何声明第三方库的`代码目录`和`资源目录`，还有该第三方库所依赖ios核心框架和第三方库。

去除所有的注释，podspec文件如下所示:

    Pod::Spec.new do |s|
      s.name     = 'ADVProgressBar'
      s.version  = '0.0.1'
      s.license  = 'MIT'
      s.summary  = 'Progress Bar Design with Percentage values.'
      s.homepage = 'https://github.com/appdesignvault'
      s.author   = { 'appdesignvault' => 'appdesignvault' }
      s.source   = { :git => 'https://github.com/appdesignvault/ADVProgressBar.git', :commit => 'f17b15c15574d6d101cd5fcfd58239e16e806647' }
      s.platform = :ios  
      s.source_files = 'ADVProgressBar/Classes/*.{h,m}'
      s.resources = "ADVProgressBar/Resources/*.png"
      s.framework = 'UIKit'

      s.requires_arc = true  
    end

其中`s.name`和`s.summary`用来声明库的名称和一个简短的说明文档。`pod search`命令就是根据这两项内容作为搜索文本的。`s.homepage`声明库的主页，`s.version`库原代码的版本，`s.license`所采用的授权版本，`s.author`库的作者。

`s.source` 声明原代码的地址，以微博sso认证登录库为例，它托管在[https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth](https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth)中，在其未尾加上`.git`扩展名就是库的原代码地址了，所以该行应声明为:

    s.source = { :git => 'https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth.git'}

对于很多第三方库而言，在发布的时候都会打上一个`tag`，如版本`0.0.1`就会打上一个名为`v0.0.1`的`tag`，但是weibo_ios_sdk_sso-oauth库还未打上所何`tag`，我们可以选择一个最新的`commit`来作为该库0.0.1版的代码。`s.source`最终如下：

    s.source = { :git => 'https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth.git', :commit => '68defea78942ecc782ffde8f8ffa747872af226d'}

以后我们可以根据该库不同的版本创建相应的podspec文件，例如`0.0.2`，`0.1.0`等。

让我们在浏览器中看一下weibo_ios_sdk_sso-oauth的目录结构:

    --
    |
    +-- demo
    |
    +-- src
    |
    +-- .gitignore
    |
    +-- README.md

`demo`目录保存一个示例项目，`src`才是库的原代码目录。`src`的目录结构如下:

    -- src
        |
        +-- JSONKit
        |
        +-- SinaWeibo
        |
        +-- sinaweibo_ios_sdk.xcodeproj
        |
        +-- SinaWeibo-Prefix.pch

`JSONKit`目录说明这个库本身依赖于`JSONKit`第三方库。我们可以在podspec文件中的`s.dependency`声明段中声明。`SinaWeibo`目录才是包含所有原代码的目录，我们需要在`s.source_files`中声明

    s.source_files = 'src/SinaWeibo/*.{h,m}'

前一部分`src/SinaWeibo/`是一个相对目录，目录的层级关系一定要跟代码库的保持一致。最后一部分`*.{h,m}`是一个类似正则表达式的字符串，表示匹配所有以`.h`和`.m`为扩展名的文件。

`src/SinaWeibo/`目录下还有一个`SinaWeibo.bundle`目录，该目录存放一些资源文件（如图片等），这些文件并不需要进行编译。可以使用`s.resourcs`声明

    s.resources = "src/SinaWeibo/SinaWeibo.bundle/**/*.png"

前一部分跟上面相同，`**`表示匹配所有子目录，`*.png`表示所有以`.png`为扩展名的图片文件。

通过查看代码我们知道，`weibo_ios_sdk_sso-oauth`还依赖一个ios的核心库`QuartzCore`

    s.framework = 'QuartzCore'

在前面我们已经说过，`weibo_ios_sdk_sso-oauth`库自身也依赖于另外一个第三方库`JSONKit`，声明如下:

    s.dependency 'JSONKit', '~> 1.4'

这行声明与Podfile文件中的声明类似。

最终的结果如下：

    Pod::Spec.new do |s|
      s.name         = "weibo_ios_sdk_sso-oauth"
      s.version      = "0.0.1"
      s.summary  = 'weibo.com sso oauth, 微博sso认证登录功能'
      s.homepage     = "https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth"
      s.license      = 'MIT'
      s.author       = {'mobileresearch' => 'mobileresearch'}
      s.source       = { :git => 'https://github.com/mobileresearch/weibo_ios_sdk_sso-oauth.git', :commit => '68defea78942ecc782ffde8f8ffa747872af226d' }
      s.platform = :ios
      s.source_files = 'src/SinaWeibo/*.{h,m}'
      s.resources = "src/SinaWeibo/SinaWeibo.bundle/**/*.png"
      s.framework  = 'QuartzCore'
      s.dependency 'JSONKit', '~> 1.4'
    end

可以将该spec文件保存到本机的`~/.cocoapods/master/`目录中仅供自己使用，也可以将其提交到CocoaPods/Specs代码库中。下面我们将其保存到本机中

    $ mkdir -p ~/.cocoapods/master/weibo_ios_sdk_sso-oauth/0.0.1
    $ cp weibo_ios_sdk_sso-oauth.podspec ~/.cocoapods/master/weibo_ios_sdk_sso-oauth/0.0.1

是否可以通过搜索找到该库:
    
    $ pod search weibo

同样在需要依赖于weibo_ios_sdk_sso-oauth这个库的项目，可以将下列添加到项目的Podfile文件中
    
    pod 'weibo_ios_sdk_sso_oauth', '0.0.1'

保存文件，并用`pod install`安装weibo_ios_sdk_sso-oauth库。

转载请注明原作者