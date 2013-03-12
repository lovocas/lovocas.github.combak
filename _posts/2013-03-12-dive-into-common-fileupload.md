---
layout: post
title: "Dive into common fileupload"
description: ""
category: java
tags: [java, fileupload]
---
{% include JB/setup %}
###fileupload简介###
apache common file upload 是一个用来接收表单形式上传的库。
表单上传就是用POST方式上传，而且Conten Type是 “multipart/form-data”类型。

我理解上的file upload: 这个包主要是处理Servlet（还有Portlet）规范的HttpServletRequest,
从request中打开一个流，并且按照[RFC 1867](http://www.ietf.org/rfc/rfc1867.txt)解析流，从
流中接收上传的文件。

看了这个项目的代码，代码量很小，貌似很久也没有更新了

###用法###
废话不多说，还是分析项目本身，对于用户来说，主要的接口就是ServletFileUpload这个类了。
这个类提供两个接口

*普通接口*

    public List /* FileItem */ parseRequest(HttpServletRequest request)
        throws FileUploadException {
             return parseRequest(new ServletRequestContext(request));
    }
fileUpload会解析request并且把接收到的文件和字段放入FileItem对象中，FileItem是对Form中每一项的抽象
所以在调用这个接口后接下来就应该遍历List,通过FileItem.isFormField();来判断是普通的字段（true）还是文件(false)
值得注意的创建ServletFileUpload对象的时候需要传入一个FileItemFactory类型,它是一个接口，只有一个待实现的方法：
   
   `FileItem createItem(
        String fieldName,
        String contentType,
        boolean isFormField,
        String fileName);

*Streaming API*

    public FileItemIterator getItemIterator(HttpServletRequest request)
        throws FileUploadException, IOException {
            return super.getItemIterator(new ServletRequestContext(request));
    }
之前的接口很简单，尤其时构造ServletFileUpload时候可以传入DiskFileItemFactory对象，并且设置相应参数，可以设置临时文件的位置，
文件存放在内存的阀值等等，但是它也有缺点，比如，接收一个很大的文件的时候，如果想把文件存储在一个地方的话，就会有两次文件操作
（接收文件一次，copy/mv文件一次），StreamAPI就可以直接处理流，比如我直接读取流，并且把文件数据写道目标文件中。

###实现###
+   实现ProgressListener可以实现服务器端进度条功能，设计上是个典型的类似button的Observer模式
+   定义RequestContext为file upload所需要的request提供的方法，ServletRequestContext delegate HttpServletRequest来实现。
+   ServletFileUpload解析的实现主要是Iterator模式的FileItemIterator, 而FileItemIterator的底层就是MultiPartStream类来处理流，比如包装读取boundary, header的方法，最底层还有LimitedInputStream 这是一个FilterInputStream, Stream包里面常用的Decorator pattern, 这里主要是来对流中数据接收大小的限制。
+   最后util包中的Strams.copy方法很好用，可以直接读取一个流到文件，不用自己去写一个循环读取了。



