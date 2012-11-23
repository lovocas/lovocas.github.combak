--- 
date: 2010-06-08 11:20:38 +08:00
layout: post
title: screen在FreeBSD系统中的安装
categories: 系统管理
tags: [screen, freebsd]
---
Screen工具是一个终端多路转接器，在本质上，这意味着你能够使用一个单一的终端窗口运行多终端的应用。

### 安装
    cd /usr/ports/sysutils/screen
    make install clean

### 使用
    screen
    //以下^A表示同按“Ctrl + A”键
    ^A c //Create，开出新的 window
    ^A n //Next，切换到下个 window
    ^A p //Previous，前一个 window
    ^A ^A //在两个 window 间切换
    ^A w //Windows，列出已开启的 windows 有那些
    ^A 0…9 //切换到第 0..9 个 window
    ^A t //Time，显示目前的时间，与系统的 load
    ^A K //kill window，强制关掉目前的 window
    ^A ? //Help，显示简单说明
    ^A d //detach，将目前的 screen session (可能含有多个 windows) 丢到背景执行
    当 按了 ^A d 把 screen session detach 掉后，会回到还没进 screen 时的状态，此时在 screen session ?每个 window 内跑的 process (无论是前景/背景)都在继续执行，即使 logout 也不影响，这个就是我喜欢的，能掉到后台去，O(∩_∩)O哈哈~

    screen -ls //显示所有的 screen sessions
    screen -r [keyword] //选择一个 screen session 回来 (捡回来) , 恢复离线的screen作业, 单独输入 screen -r 也行的.

### 补充说明：
    screen是一个多重视窗管理程序，此处所谓的视窗，是指一个全屏幕的文字模式画面。通常只有在使用telnet登入主机或是使用老式的终端机时，才有可能用到screen程序.

    在linux应用screen命令应该差不多吧！
    me在连接到dreamhost.com的空间那里时，尝试输入screen , 居然存在这命令啊，爽，以后在dreamhost又多一样玩意了，看来dreamhost的主机不错.

