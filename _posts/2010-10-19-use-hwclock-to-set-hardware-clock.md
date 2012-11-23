--- 
date: 2010-10-19 08:10:29 +08:00
layout: post
title: !binary |
    55SoaHdjbG9ja+iuvue9ruehrOS7tuaXtumXtA==

wordpress_id: 478
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDc4

---
0. 用date看系统时间，用hwclock --show看硬件时间。

1. 先用ntpdate找一个时间服务器把系统时间调整回来。www.pool.ntp.org提供了很多时间服务器，当然也包括中国的。命令如下，
sudo ntpdate 0.cn.pool.ntp.org

2. 用hwclock把系统时间写入到硬件时间（如果只修改系统时间，每次开机系统时间还会改回硬件时间），命令如下，
sudo hwclock --systohc

3. 让系统定期从时间服务器更新一下时间。配置/etc/ntpd.conf和/etc/conf.d/ntp-client，然后可以把ntp-client和ntpd加入default run level，命令如下，
sudo rc-update add ntpd default
sudo rc-update add ntp-client default

更简单的方法，在/etc/conf.d/clock里把CLOCK/TIMEZONE设置正确的前提下，把下面的CLOCK_SYSTOHC设置成yes，这样系统关机时会自动把时间写回硬件。
