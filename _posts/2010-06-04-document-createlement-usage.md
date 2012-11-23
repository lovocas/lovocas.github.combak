--- 
date: 2010-06-04 16:56:22 +08:00
layout: post
title: !binary |
    ZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgp55qE55So5rOV

wordpress_id: 349
wordpress_url: !binary |
    aHR0cDovL3d3dy5pc3BocC5vcmcvYi8/cD0zNDk=

---
document.createElement()是在对象中创建一个对象，要与appendChild() 或 insertBefore()方法联合使用。其中，appendChild() 方法在节点的子节点列表末添加新的子节点。insertBefore() 方法在节点的子节点列表任意位置插入新的节点。

      下面，举例说明document.createElement()的用法。
[code lang="html"]
<div></div>
[/code]
例1：
[code lang="javascript"]
        
[/code]
效果：在标签board中加载一个按钮，属性值为“这是测试加载的小例子”。
例2：
[code lang="javascript"]
        
[/code]
效果：在标签board中加载一个下拉列表框，属性值为“加载项1”和“加载项2”。
例3：
[code lang="javascript"]
        
[/code]
效果：在标签board中加载一个文本框，属性值为“使用setAttribute”。 当点击这个文本框时，会弹出对话框“This is a test!”。
        根据上面例子，可以看出，可以通过加载对象的属性来设置，参数是相同的。使用e.type="text" 和 e.setAttribute("type","text")效果是一致的。
        下面，我们用实例来讲述一下appendChild() 方法和insertBefore() 方法的不同。
        比如我们要在下面这个div中插入一个子节点P时：[code lang="html"]<div>NodeNode</div>[/code]
我们可以这样写：
[code lang="javascript"]

[/code]
      通过以上的代码，可以测试到一个新的节点被创建到了节点div下，且该节点是div最后一个节点。很明显，通过这个例子，可以知道appendChildhild和insertBefore都可以进行插入节点的操作。
　　 在上面的例子中有这样一句代码：oTest.insertBefore(newNode,null) ，这里insertBefore有2个参数可以设置，第一个是和appendChild相同的，第二却是它特有的。它不仅可以为null，还可以为：
[code lang="javascript"]

[/code]
效果：这个例子将在x1节点前面插入一个新的节点
又或：
[code lang="javascript"]

[/code]
效果：这个例子将在x1节点的下一个节点前面插入一个新的节点

还可为：
[code lang="javascript"]

[/code]
　　这个例子将在第一子节点前面插入一个新的节点，也可以通过改变childNodes[0,1,...]来在其它位置插入新的节点

由于可见insertBefore()方法的特性是在已有的子节点前面插入新的节点，但例一中使用 insertBefore()方法也可以在子节点列表末插入新节点的。两种情况结合起来，发现insertBefore()方法插入节点，是可以在子节点列表的任意位置。
　　从这几个例子中得出：

　　appendChild() 方法在节点的子节点列表末添加新的子节点。
　　insertBefore() 方法在节点的子节点列表任意位置插入新的节点。
