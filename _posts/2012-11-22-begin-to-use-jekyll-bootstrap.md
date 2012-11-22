---
layout: post
title: "开始使用Jekyll Bootstrap"
description: "开始使用Jekyll Bootstrap做为主要的博客管理工具，介绍一下安装方法"
category: Blog
tags: [blog,life]
---
{% include JB/setup %}
## 起因

  无意中发现[Jekyll Bootstrap](http://jekyllbootstrap.com/)，简单看了一下介绍，发现不管是安装还是使用都非常方便，于是决定尝试安装一下。

## 介绍

  [Jekyll Bootstrap](http://jekyllbootstrap.com/)是一个为开发者和设计者开发的一款轻型博客框架。此程序原生支持Github Pages服务，借助Pages服务，可以使博客免费的托管于Github平台。而在Git的管理之下，文章的修改历史，版本等都会完整的保存，所以，使用JB是非常有意义的。

## 准备

  因为JB是用Ruby编写，使用Rake构建的，所以，Ruby是必须的。
  因为文章是托管于Github的，所以，Git也是必须的，可以自行查文档安装。
  因为在与Github的通信中会使用密钥对的方式对权限进行验证，所以，你需要有一个具有ssh的环境，如果你使用的是MacOSX/Unix/Linux/BSD，那么你已经有了SSH环境，这一步可以不管，如果使用Windows，那至少要装个cygwin之类的模拟环境，然后装上ssh就可以了。

## 安装
    
  在安装好Ruby和Git之后就可以开始安装JB了。
  首先，在[Github](http://www.github.com)申请帐号，然后创建公开代码库。
  其次，用ssh-keygen生成密钥文件，把对应的公钥文件导入Github，在Github的用户设置里做相应的设置。
  第三
    $ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
    $ cd USERNAME.github.com
    $ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
    $ git push origin master
  第四、完成

## 后记

  以后在使用过程中就可以用rake post title='Post Title'之类的发布就可以了。
