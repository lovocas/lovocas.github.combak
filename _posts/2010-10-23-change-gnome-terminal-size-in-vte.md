--- 
date: 2010-10-23 11:13:14 +08:00
layout: post
title: !binary |
    6YCa6L+HdnRl5L+u5pS5R25vbWUtVGVybWluYWzliJ3lp4vnqpflj6PlpKfl
    sI8=

wordpress_id: 487
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDg3

---
编辑/usr/share/vte/termcap/xterm文件

将其中的xterm-xfree86|xterm-new|xterm terminal emulator (XFree86):项目的co和li设置为自己需要的值，例如我的设置是这样的
[code lang='bash']
xterm-xfree86|xterm-new|xterm terminal emulator (XFree86):
:am:km:mi:ms n:
:co#100:it#8:li#40:
[/code]

其中co#100表示宽度100个字符，li#40表示40行高。经过这样的设置后所有的xterm类程序都会以100×40的缺省尺寸打开包括xterm,gnome-terminal，rxvt……
