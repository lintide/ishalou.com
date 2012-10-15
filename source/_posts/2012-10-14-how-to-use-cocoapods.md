---
layout: post
---

使用CocoaPods管理iOS的第三方类库
==============================

iOS第三方类库的管理是一个很麻烦的事，项目信赖的类库和版本很难控制。让[CocoaPods](https://github.com/CocoaPods/CocoaPods) 来帮帮我们吧。

## 安装 ##

先确认自己是否安装了ruby的运行环境，若没有则安装之。接着：

    $ gem install cocoapods
    $ pod setup

## 使用 ##

- 用Xcode新建一个iOS新项目，创建后目录结果如下：（项目名为：App)

        App
          |
          +- App
          |
          +- App.xcodeproj


- 进入顶层App目录

        $ cd ~/App

- 新建一个名为 Podfile 的文件

        $ touch Podfile
        $ vi Podfile

    输入以下内容，并保存

    > platform :ios
    > 
    > pod 'JSONKit',     '~>1.4'
    >
    > pod 'Reachability',     '~>2.0.4'

        $ pod install

    cocoaPods将自动从服务器中拉取相应的第三方库原代码，将其存放在Pods目录中。

- 现在目录结构如下:

        App
          |
          +- App
          |
          +- App.xcodeproj
          |
          +- App.xcworkspace
          |
          +- Podfile
          |
          +- Podfile.lock
          |
          +- Pods


    其中 `Pods`目录是一个xcode项目，里面包含所有在Podfile中声明的第三方库代码。`App.xcworkspace` 为xcode的工作空间文件，以后用这个文件来打开项目。

        $ open App.xcworkspace

- Podfile.lock 文件记录所有已安装的代码库的描述，文件如下：

        PODS:
        - JSONKit (1.5pre)
        - Reachability (2.0.5)

        DEPENDENCIES:
        - JSONKit (~> 1.4)
        - Reachability (~> 2.0.4)

        SPEC CHECKSUMS:
         JSONKit: a01a22c75f27eae76b4badd55a91c20fe6e86477
         Reachability: 8d29c8365f72967b6decc8d2892a7d5dc6550799


    不要更改这个文件的内容。如果要添加或者删除代码库，在Podfile文件里增加或删除pod声明就可以了。现在第三方库管理起来是不是方便多了呢？
