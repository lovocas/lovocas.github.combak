--- 
date: 2010-06-04 16:43:15 +08:00
layout: post
title: IE与Firefox在Javascript上的区别的几小点
categories: 前端开发
tags: [ie, firefox, javascript]
---
1、innerText和textContent
这两个属性功能相同，前者用于IE，后者用于Firefox
例：

    if(document.all) {
        document.getElementById('element').innerText = "text";
    }else {
        document.getElementById('element').textContent = "text";
    }

2、获取键盘输入字符keyCode
在IE下可以直接使用event.keyCode，但是在Firefox下不能这样使用，在FF中会往onkeyup之类的事件中传入隐含的参数e，可以用which和charCode（Opera支持which和keyCode)，可以这样：

    element.onkeyup = function(e) {
        var code = 0,e = e||event;
        code = e.keyCode||e.which||e.charCode;
        return code;
    }

3、其它的一些Javascript
动态设置HTML控件属性

    obj.setAttribute('name', 'val');

禁止INPUT输入框的自动完成功能
也可以在脚本中动态设置

    obj = document.getElementById('uname');
    obj.setAttribute("autocomplete","off");

在脚本中直接给控件设置样式表，之前一直是用`obj.style.color="#000"`类似的方法设置控件的CSS属性，一行代码一个属性，太麻烦了，这有一个cssText可以方便的设置HTML控件的样式

    var obj = document.getElementsByTagName("DIV");
    obj[0].cssText = "width:100%;height:50%;border:1px #000 solid;"

