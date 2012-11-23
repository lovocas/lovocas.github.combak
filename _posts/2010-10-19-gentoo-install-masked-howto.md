--- 
date: 2010-10-19 21:30:53 +08:00
layout: post
title: !binary |
    R2VudG9v5Lit5aaC5L2V5a6J6KOFTWFza2Vk55qE6L2v5Lu2

wordpress_id: 483
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDgz

---
在Gentoo的Portage系统中，经常可以看到Masked的软件，一般是指在当前系统中，或因USE设置而不被安装的标记，那么当需要安装这些被标记的软件时应该怎么办哪。
比如在64系统中安装fcitx，默认是于64位不稳定的，此时则需要做以下操作
[code lang='bash']
echo "=app-i18n/fcitx-3.6.3" >> /etc/portage/package.unmask
[/code]
此时执行
[code lang='bash']
emerge -pv fcitx
[/code]
会发现还有一行被~amd64所标记，那么
[code lang='bash']
echo "=app-i18n/fcitx-3.6.3 ~amd64" >> /etc/portage/package.keywords
[/code]
此时再执行
[code lang='bash']
emerge fcitx
[/code]
软件正常安装
此处需要注意的是，在echo语句中，软件要写全路径及版本号。
