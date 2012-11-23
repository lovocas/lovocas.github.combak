--- 
date: 2011-02-22 20:56:00 +08:00
layout: post
title: !binary |
    TUFJTEFEVijpgq7ku7blub/lkYrlt6Xlhbcp

wordpress_id: 715
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnLzIwMTEvMDIvbWFpbGFkdi1yZWxlYXNl
    Lmh0bWw=

---
公司要给会员群发一批邮件，没有找到合适的现成的工具，就自己写了一个，先是用Python写了一个，用起来感觉不是很爽，于是干脆用C重写了一个，好久没用C语言了，该是再拿起来的时候了，说起来还是C语言用着感觉好。

源代码：<a>https://github.com/tobylee/mailadv</a>

直接下载：<a>mailadv-0.9.tar.bz2</a>

程序说明：

本程序使用标准C编写，目前在FreeBSD和Linux（包括Gentoo/Debian/CentOS）进行
编译测试，完全兼容，在Windows下使用可能需要做一些修改，比如发送邮件时使用
sendmail.exe。

文件包内的Makefile只支持对较新版本的make兼容。

功能说明：

mailadv [-r] [-h] [-f < 发件邮箱>] [-t < 接收地址>] [-m < 邮件列表>] [-b < 邮件文件>] [-s < 邮件标题>] [-l < 日志文件>]

    -f    --from      发件邮箱. 默认: A.D.S <ads @ads.snowpear.com>
    -t    --to        收件邮箱，本设置可以和-m同时使用，但是本设置中的邮件发送情况不记入日志文件.
    -m    --maillist  邮件列表文件，每行一个邮件地址. 默认: 当前目录下的maillist
    -b    --content   邮件内容文件，需要HTML格式的文件. 默认: 当年目录下的mail.htm
    -s    --subject   邮件标题，此项是必须的.
    -l    --log       日志文件, . 默认: 当年目录下的maillog
    -r    --continue  继续执行上一次未发送完的邮件列表, 此功能以发送日志为判断依据.
    -h    --help      显示帮助信息.
