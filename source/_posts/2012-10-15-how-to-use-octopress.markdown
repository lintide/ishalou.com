---
layout: post
title: "使用Octopress + github管理blog"
date: 2012-10-15 02:44
comments: true
categories: 
---

## 准备 ##

用Octopress来管理blog是要点耐心的，我就有几次想要放弃，因为涉及的概念很多。当然对于头上顶着hacker这个词的人，再难的困难都能克服。

要使用octopress和github来管理blog，你得懂一点点`ruby`(jekyll), 并且使用过`github`,然后还要懂一些`git`命令，学习`Markdown`来写文章。就这些，言归正传。(注：如果你输入下列的命令发现该命令未找到的时候，请自行google并安装之，恕不一一介绍)。确保你的系统已经安装了下列程序

- git
- ruby (1.9.3) 和 rvm 和 (bundle)


## 安装 ##
    
    $ git clone https://github.com/imathis/octopress.git myblog

注意，后面的`myblog`你可以取任何你想要的名称。

让我们来看看这个目录里现在包含了什么:

    $ cd myblog
    $ ls

显示如下：

    CHANGELOG.markdown  README.markdown     config.rb
    Gemfile         Rakefile        config.ru
    Gemfile.lock        _config.yml     plugins

先不用理解这些文件都代表什么意思，继续:

    $ bundle install

安装octopress信赖的ruby库，完成后进行初始化:

    $ rake install

现在看看目录里有哪些文件

    $ ls

显示如下：

    CHANGELOG.markdown  Rakefile        plugins
    Gemfile         _config.yml     public
    Gemfile.lock        config.rb       sass
    README.markdown     config.ru       source

我们看到，系统新增了两个文件夹，`public`和`source`。`source`文件夹就是我们以后工作的目录，`source`中有个`_posts`文件夹，这就是保存每一篇blog的地方了；而对于`public`文件夹，我们不需要直接操作它，它将由系统自动生成。

现在我们可以新增blog了。

    $ rake new_post["hello world"]

这个命令将在 `source/_posts/`文件夹下生成一个类似`yyyy-mm-dd-hello-world.markdown`的文件，其中`yyyy-mm-dd`为系统当前的日期，而扩展名表示这是个`Markdown`文件。你可以选择你喜欢的文本编辑器来修改这个文件。强烈推荐`Sublime Text 2`。将下列文本添加到该文件的末尾中，先不管其中的符号代表什么.

    ## Hello world ##

来看看我们的成果吧

    $ rake preview

Octopress在本机中开启了一个服务器，打开浏览器看看[http://localhost:4000](http://localhost:4000)。一切正常:) 

如果你现在打开`public`这个文件夹，将会看到很多新增的文件。这些文件不需要我们的管理，当然理解它也是有意义的。

## 在github中托管blog  ##

到目前我止，我们的blog仅能在本机中访问，如何将其托管到github中呢？如何使用我们自己的域名呢？让一切看起来就如同我们自己的站点。

如果你要使用自己的域名，github需要一个CNAME文件，进入`source`文件夹，新建`CNAME`文件。
    
    $ ch source
    $ touch CNAME

将你的域名写入`CNAME`文件中，如`myblog.com`。你还需要到你的域名管理中心中新增一个`A Record`，并指向`207.97.227.245`IP。

托管到github中

    $ rake setup_github_pages

系统提示你输入一个形如:`git@github.com:username/myblog.com.git`,`username`是你在github.com中注册的用户名，`myblog.com.git`是你自己在github.com中创建的代码库。输入后按回车确定.

现在再看看我们的文件夹:

    $ ls

系统新增了一个文件夹`_deploy`，其实这个文件夹本身也是一个git代码库，你可以使用`ls -a`命令，将看到一个名为`.git`的文件夹，这个文件夹表示这是个git库（不要修改里面的内容）。
现在可以向github提交我们的网站了.

    $ rake generate
    $ rake deploy

如果提示如下错误

    ## Pushing generated _deploy website
    ERROR: Repository not found.
    fatal: The remote end hung up unexpectedly

则说明你还没有在github.com中创建形如`myblog.com`的代码库，到github中创建之。如果一切正常那么恭喜你:)

使用域名访问是否正常。

让我们回过头来到github.com上看一下`myblog.com`代码库，如果你仔细观察，将会发现这些新增的代码保存在`gh-pages`分支中（左上角会显示: `branch: gh-pages`)。这里的代码其实是我们本地计算机`_deploy`文件夹中的一份拷贝，还记得我们在上面说过吗`_deploy`其实也是一个git库。

仅仅托管这个文件夹还是不够的，因为我们自己产生的所有文件将保存在`source/_posts`文件夹中，而顶级目录`myblog`下也有我们自己的配置文件。我们想把这些都加入代码库中，因为这样才能真正的获得版本控制的好处。你想对你的文章怎么修改都行，只要你向服务器提交了更改，总可以回退到以前的某一个版本。

那么？要怎么做才合理呢？还是向大师学习吧，`Octopress`的大师是谁？那就是Octopress自身了，我们到[https://github.com/imathis/octopress](https://github.com/imathis/octopress)上看看吧，点击左上角`branch: master`的向下按钮，你们看到很多个brach的名称，其中就有`gh-pages`分支，还有`master`、`site`等等。点击`site`这个分支看看。是不是跟我们本地的`myblog`文件夹很像呢？也有`source`，`plugins`,`sass`等文件夹和`_config.yml`等配置文件。不过少了两个文件`_deploy`和`public`，这两个文件夹恰恰就是我们不需要进行版本控制的，因为`_deploy`就是我们在`gh-pages`分支中的代码，而`public`是用于我们在本地机器预览时用到的。那它是怎么做到的呢？让我们来看看究竟。

进入`myblog`这个目录,然后

    $ ls -a

哦，好多文件。其中以`.`开头的是隐藏文件，当使用`ls`命令时是无法看到。对我们而言，重要的是`.git`文件夹和`.gitignore`文件。上面我们说过`.git`文件夹表明这是个git代码库。`.gitignore`是用来声明哪些文件或文件夹不需要加入git版本控制中。看看它里面有什么:

    $ cat .gitignore

显示如下：

    .bundle
    .DS_Store
    .sass-cache
    .gist-cache
    .pygments-cache
    _deploy
    public
    sass.old
    source.old
    source/_stash
    source/stylesheets/screen.css
    vendor
    node_modules

里面显示的文件或文件夹都不会出现在git代码库中，其中就有`_deploy`和`public`，这些正是我们所需要的。拿来即用。看看当前的代码库的状态：

    $ git status

将显示有哪些文件未加入代码库中，可以使用`git add`和`git commit`命令。如果对`git`不是很理解，建议看[git简易指南](http://rogerdudler.github.com/git-guide/index.zh.html)

如何将这个代码库也向Octopress一样提交到我们自己的`site`分支中呢?

    $ git remote add origin git@github.com:username/myblog.com.git

`git@github.com:username/myblog.com.git`必须跟你自己在上面使用的代码库地址一致。

    $ git branch -m site
    $ git push origin site

一切正常:),看看github中的代码库，现在是不是有两个分支了呢？`gh-pages`和`site`。

## 发表blog的流程 ##

1. 用`rake new_post["new post name"]`命令新建blog文章.
2. 用Sublime Text 2编辑新建的blog.
2. `rake preview`在本机中预览。
2. `rake generate`和`rake deploy`上传到github.com
2. 同时用`git add`, `git commit`, 和`git push origin site` 提交site分支的更改.

就这些...

## 参考文档 ##

[octopress](http://octopress.org/)

[github pages](http://pages.github.com/)

[Blog = Github + Octopress](http://mrzhang.me/blog/blog-equals-github-plus-octopress.html)

[git简易指南](http://rogerdudler.github.com/git-guide/index.zh.html)

[像黑客一样写博客](http://kyle.xlau.org/posts/blogging-like-a-hacker.html)

