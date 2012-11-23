--- 
date: 2010-11-08 08:57:08 +08:00
layout: post
title: !binary |
    R05PTUXoj5zljZXorr7nva7ku6Xlj4rlupTnlKjnqIvluo/lhbPogZQ=

wordpress_id: 500
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NTAw

---
1. 这些文件与gnome菜单条目及桌面文件的打开方式有关：
/usr/share/applications/*   //这里存放所有菜单相关的应用程序配置
~/.local/share/applications/*   //这里存放某用户已启动的应用程序缓存

/usr/share/applications/mimeinfo.cache
~/.local/share/applications/mimeinfo.cache

如果执行：sudo update-desktop-database
则生成 /usr/share/applications/mimeinfo.cache
如果执行：update-desktop-database ~/.local/share/applications
则生成 ~/.local/share/applications/mimeinfo.cache

2. 在'~/'中修改打开方式不对的文件类型：
比较对的打开程序的'~/.local/share/applications/打开程序名.desktop'和'/usr/share/applications/打开程序名.desktop'，将需要的'MimeType'复制到'~/...'文件中。
将错的打开程序的'~/.../xx.desktop'中的'MimeType'不该有的内容删除。
执行：update-desktop-database ~/.local/share/applications使改动生效。
这样用局部设置覆盖对应的全局设置。

3. 如果要删除gnome菜单的一个条目，直接删/usr/share/applications/或~/.local/share/applications/下对应的desktop文件即可。
* 如果desktop文件中有'NoDisplay=true'表示该条目不在GNOME菜单中出现，'=false'或没
有此行则会出现。
