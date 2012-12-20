---
layout: post
title: maildrop原理
tags:
- 简单生活
---
maildrop是一个替代邮件代理并且包含邮件过滤的语言，系统管理员用

这个maildrop即可以取代已经存在的邮件投递代理，或者用户可以用已

经存在的邮件投递代理的&rsquo;forward to

program&rsquo;机制来取代来运行maildrop.

maildrop首先从标准输入读取E-mail信息,其后面所带的回车符号将自动

被删除，一个E-mail信息包含header lines 后面跟着一个空行，然后跟

着信息的内容，信息的内容也可能在第一个header lines

之前包含mbox-style From_ line,如果信息内容没有包含From_line,那

么maildrop将自动创建一个.

如果文件/etc/maildroprc存在，邮件投递或邮件过滤将从/etc/maildro

prc中读出。maildrop的投递/过滤指令可能把信息保存在一个特殊的mai

lbox,或者丢弃它，或者返回给发送者，或者转发到不同的邮件地址。

如果文件/etc/maildroprc不存在，或者邮件投递或邮件过滤没有完全的

处理信息，这时maildrop将从$HOME/.mailfilter读取规则，如果.mailf

ilter不存在，或者邮件投递或邮件过滤没有完全的处理信息,maildrop

将保存E-mail信息到默认的mailbox里。

<!--more-->maildrop知道怎样投递到一个标准的mailbox文件，也知道怎样投递到一

个maildirs,maildir格式的目录在Courier和qmail中使用，还有一些邮

件服务器也知道如何去读maildirs。当投递mailbox文件时，maildrop将

锁定mailbox,直到投递结束。

maildrop读取信息的规则为：在From_header之前致少一空行，maildrop

在读取规则时将忽略此空行,因此maildrop要求规则中至少有一个空行。
 通常邮件投递模式分为三种（maildrop依赖投递模式的不同也有小小的

差异）：
 1.手动模式.
 一个文件包含过滤指令 例：maildrop -filename(保存过滤指信令的文

件),首先maildrop读取/etc/maildroprc文件里面的指令，然后从filena

me读取过滤指令.如果filename里面没有转发，回复，删除或到一个特殊

的maildox,那么它将投递到系统用户的mailbox中.

2投递模式.
 maildrop如果没有指定 -filename时，maildrop将在投递模式运行，maildrop将改变当前目录到用户的的主目录，这时读取/etc/maildroprc,再读取 $HOME/.mailfilter

3 嵌入模式
 主要用于Courier上，在此不做阐述.

安全
 maildrop在setuid位下安装是安全的。Courier为了让maildrop在嵌入模式下使用因此它的setuid位是默认的。如果root 运行maildrop 则-d这个选项会被用来指定这个信息的接受者，maildrop立马会重制用户的ID,读取$HOME/.mailfilter文件，并将信息投递到指定的用户。

系统管理员能通过配置来限制maildrop -d 这个选项是否给每个用户来使用（邮件系统用户本伸除外）
 maildrop的（简单）用法：
 -A &ldquo;Header:value&rdquo;
 增加一个附加的头部信息。
 -d user
 在投递模式设置用户的ID
 -f address
 设置变量&rdquo;FROM&rdquo;为一个地址
 -m
 在嵌入模式下运行
 -M filterfile
 在一个特殊的嵌入模式运行
 cc dotlock flock log logfile to xfilter
 都不能运行在嵌入模式下
 （待续）

参考：
 http://bbs.chinaunix.net/thread-304134-1-1.html
 http://www.extmail.org/forum/thread-6495-1-1.html