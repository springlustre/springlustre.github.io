---
layout: post
title: gitbook安装使用教程及和github同步方法
excerpt: "gitbook安装使用教程及和github同步方法"
categories: 环境搭建
tags: 
- 环境搭建
- gitgook

comments: true
---

* Table of Contents
{:toc}

gitbook(www.gitbook.com)是一个网络出版平台，作者可以方便的在上面撰写书籍的章节，然而整理成册，形成电子书。GitBook是一个命令行工具，我们可以借用该工具使用Github和Markdown来制作图书。


GitBook支持输出多种文档格式，如静态站点，PDF,eBook,Json等。


使用gitbook的方式如下：

## 1、安装npm

登录http://nodejs.org/ 下载nodeJs安装包，直接双击安装即可。node.js已经集成了npm，直接在命令行输入 
```

npm -v

```


## 2、安装gitbook

输入

```

npm install -g gitbook-cli

```

即可。


## 3、创建gitbook

登录github 创建一个仓库作为书籍的工程。


 ![这里写图片描述](http://img.blog.csdn.net/20161028231716561?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


使用github的账号授权登录gitbook（https://www.gitbook.com）

 ![这里写图片描述](http://img.blog.csdn.net/20161028232228873?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


    创建gitbook，选择从github导入，选择github中相应的仓库

 ![这里写图片描述](http://img.blog.csdn.net/20161028235020714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 ![这里写图片描述](http://img.blog.csdn.net/20161028235039698?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


## 4、gitbook工具的使用

SUMMARY.md 是一本书的目录结构，主要目录结构如下


```
* [Chapter1](chapter1/test1.md)
    * [Section1.1](chapter1/test1.md)
    * [Section1.2](chapter1/test1.md)
* [Chapter2](chapter2/test2.md)

```

然后在命令行输入 


```
gitbook init

gitbook serve

```


即可通过本地4000端口访问，预览生成的gitbook。


## 5、书籍的上传和同步

将github仓库克隆到本地，编辑好之后上传到github，便会自动同步到gitbook，点击gitbook书籍主页的read即可阅读。

