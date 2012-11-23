--- 
date: 2010-09-22 09:59:12 +08:00
layout: post
title: !binary |
    SUU45Lit55qEaW5uZXJUZXh05bGe5oCn

wordpress_id: 411
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDEx

---
innerText在IE7之前的版本中都是直接获取元素内纯文本内容的属性，如
[code lang='js']
tTxt = obj.innerText;
[/code]
这等同于在Firefox及CHROME中的textContent属性，但是这一行为在IE8中发生了变化。
在IE8中innerText属性只能读取块级元素的内容，而且要读取的元素必须同时具有开始和关闭标签，否则是不具有innerText属性的。
同时innerText在html,table,tbody,tfoot,thead和tr对象中是只读的。

如上面所述，在默认情况下innerText是不能使用在li对象中的，这时可以在CSS中明确设置LI的display属性为block，此时在程序中不用任何设置就可以读取此对象的innerText。
当然，如果LI中只是纯文字，那么可以直接用innerHTML代替innerText也是没有问题的。
