--- 
date: 2010-11-09 18:01:34 +08:00
layout: post
title: !binary |
    VmlydHVhbEJveCDml6Dms5XlkK/liqjvvJpWQm94Vk1NLnNvOiBjYW5ub3Qg
    b3BlbiBzaGFyZWQgb2JqZWN0IGZpbGUg55qE6Kej5Yaz5Yqe5rOV

wordpress_id: 520
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NTIw

---
简介：
VirtualBox 无法启动：VBoxVMM.so: cannot open shared object file 的解决办法


今天更新了 glibc 以后发现 VirtualBox 不能用了， 提示：

$ VirtualBox
VirtualBox: supR3HardenedMainGetTrustedMain: dlopen("/opt/VirtualBox/VirtualBox.so",) failed: VBoxVMM.so: cannot open shared object file: No such file or directory

Google 了一下，发现有人说是 glibc 引起来的，并给出了解决方案 —— 通过 chrpath 来修改动态库的位置：
[code lang="bash"]
cd /tmp
wget http://www.tux.org/pub/x/ftp.hungry.com/chrpath/chrpath-0.13.tar.gz
tar -zxvf chrpath-0.13.tar.gz
cd chrpath-0.13
./configure && make
cd /opt
ln -s VirtualBox VB
cd VirtualBox/
/tmp/chrpath-0.13/chrpath -r /opt/VB *.so
[/code] 
