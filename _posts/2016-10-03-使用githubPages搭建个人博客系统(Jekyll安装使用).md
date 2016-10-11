---
layout: post
title: 使用githubPages搭建个人博客系统(Jekyll安装使用)
excerpt: "使用githubPages搭建个人博客系统(Jekyll安装使用)"
categories: 环境搭建
tags: 
- huanj
- git
- jekyll
comments: true
---

* Table of Contents
{:toc}


github Pages 是在github上托管的静态网页。从2016年6月15日开始，通过域名github.io访问使用的是https协议。github pages的代码仓库容量限定是1G,并且带宽限制是100G或者每月10万次访问。

使用github搭建个人博客步骤如下：


## 创建github仓库

在github上创建github pages的仓库，如果名字是xxx 则仓库的名字是xxx.github.io, 之后提交代码到这个代码仓库，访问https://xxx.github.io 即可以访问你的主页。

![Elaphurus davidianus](http://img.blog.csdn.net/20161010221801201?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



## 安装ruby环境

下载安装包  <a href="http://rubyinstaller.org/downloads/"> http://rubyinstaller.org/downloads/ </a> 安装时记得勾选“加入环境变量的选项”，安装完在命令行输入 ruby -v  出现下图即安装成功


![Elaphurus davidianus](http://img.blog.csdn.net/20161005224528497?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

下载devkit  <a href="https://github.com/oneclick/rubyinstaller/downloads/">https://github.com/oneclick/rubyinstaller/downloads/ </a>下载最新版devkit，双击会提示选择路径自动解压，然后进入到解压路径，输入以下命令

 ruby dk.rb init      

 ruby dk.rb install

 ![Elaphurus davidianus](http://img.blog.csdn.net/20161005224550701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


## 安装Jekyll

首先加入国内的镜像，因为国外的镜像有的连接不上。

gem sources --remove https://rubygems.org/

![Elaphurus davidianus](http://img.blog.csdn.net/20161005224605248?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

gem source --add http://gems.ruby-china.org/

![Elaphurus davidianus](http://img.blog.csdn.net/20161005224617813?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

接着使用命令 gem install jekyll 开始安装Jekyll

![Elaphurus davidianus](http://img.blog.csdn.net/20161005224631060?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

安装完后输入 jekyll -v 显示版本号即安装成功。

![Elaphurus davidianus](http://img.blog.csdn.net/20161005224643657?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


## 安装Rdiscount

Jekyll默认的markdown解析器maruku对中文支持不够完善,所以果断换成RDiscount解析器

输入 gem install rdiscount

![Elaphurus davidianus](http://img.blog.csdn.net/20161005224655111?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)




### 创建jekyll工程，开始使用

输入 jekyll new blog , 创建名字为blog的工程

进入blog目录，运行 jekyll serve 命令，会启动服务，访问localhost:4000即可预览。


![Elaphurus davidianus](http://img.blog.csdn.net/20161006135055590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


## 提交到github

将上述工程的代码复制到 在本地克隆的xxx.github.io的git仓库下，提交push到github，即可访问。


## 其它

如果有报错，比如下面之类的


```
E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/resolver.rb:366:in `block in verify_gemfile_dependencies_a
re_found!': Could not find gem 'jekyll-sitemap x86-mingw32' in any of the gem sources listed in your Gemfile or availabl
e on this machine. (Bundler::GemNotFound)
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/resolver.rb:341:in `each'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/resolver.rb:341:in `verify_gemfile_dependenci
es_are_found!'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/resolver.rb:199:in `start'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/resolver.rb:181:in `resolve'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/definition.rb:250:in `resolve'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/definition.rb:174:in `specs'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/definition.rb:233:in `specs_for'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/definition.rb:222:in `requested_specs'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/runtime.rb:118:in `block in definition_method
'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler/runtime.rb:19:in `setup'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/bundler-1.13.2/lib/bundler.rb:99:in `setup'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/jekyll-3.2.1/lib/jekyll/plugin_manager.rb:36:in `require_from_bundler'
        from E:/Ruby23/lib/ruby/gems/2.3.0/gems/jekyll-3.2.1/exe/jekyll:9:in `<top (required)>'
        from E:/Ruby23/bin/jekyll:23:in `load'
        from E:/Ruby23/bin/jekyll:23:in `<main>'

```

则因为缺少相应的模块，根据提示“Could not find gem 'jekyll-sitemap x86-mingw32' in any of the gem sources listed”安装相应模块即可。即输入 gem install jekyll-sitemap


![Elaphurus davidianus](http://img.blog.csdn.net/20161006135550473?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

以此类推，如果遇到类似错误，则缺少什么模块安装什么模块即可。
