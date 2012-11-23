--- 
date: 2010-07-06 15:06:23 +08:00
layout: post
title: !binary |
    Y3lnd2lu5Lit5paH6YWN572u

wordpress_id: 373
wordpress_url: !binary |
    aHR0cDovL3d3dy5pc3BocC5vcmcvYi8/cD0zNzM=

---
cygwin 1.7版本中文配置方法：
添加LANG=zh_CN环境变量，可在系统属性添加，也可以直接添加到cygwin的启动脚本里面，脚本修改后为：
[code lang="plain"]
    @echo off
    set CYGWIN=tty notitle glob
    set LANG=zh_CN
    C:
    chdir C:cygwinbin

    bash –login –i
[/code]
主要添加了两个环境变量。

这样就可以了，不用修改什么ls，less，甚至是inputrc都不用，直接就完美支持中文。

可参考：http://cygwin.com/cygwin-ug-net/setup-locale.html
            Cygwin 1.7版 中文问题的解决（99%）

git子命令自动完成
cygwin安装完成后，默认是没有设置好git的completion的。设置方法在/etc/bash-completion.d/git
里面，也就是git的completion脚本。
（1）拷贝/etc/bash-completion.d/git文件到某个地方，如：~/.git-completion.sh
（2）添加以下内容到.bashrc或者.bash_profile，具体位置具体修改
[code lang="bash"]
    source ~/.git-completion.sh
[/code]
重启cygwin即可。
