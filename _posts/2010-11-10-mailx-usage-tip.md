--- 
date: 2010-11-10 13:41:00 +08:00
layout: post
title: !binary |
    bWFpbHjkvb/nlKjngrnmu7Q=

wordpress_id: 527
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NTI3

---
mailx，曾命名nail，是一个比较小型的电子邮件发送和读取的程序，slackware下mail命令就是用它。

在默认的情况下， 它将邮件发往本地的MTA(Mail Transfer Agent)（比如使用Sendmail、Qmail、Postfix、等提供的服务）

它的全局配置放在/etc/nail.rc，用户个人配置放在$HOME/.mailrc。
 要配置用外部SMTP服务器发信，在个人的  .mailrc    添加内容：
 set from=user@tom.com smtp=smtp.tom.com
 set smtp-auth-user=your_E-mail_address smtp-auth-password=your_password
 set smtp-auth=login
 说明：from                   就是发送者的邮件地址，就是你自己的一个可用的邮件地址。
 smtp                   是指提供邮件服务的主机，比如smtp.tom.com; smtp.126.com等
 smtp-auth-user       是指你在服务主机上的邮箱用户名
 smtp-auth-password 当然是你在smtp主机上的密码
 smtp-auth              验证方式了

因为配置中提供的密码是明文的，所以最好 chmod 600 .mailrc，以免泄密。

发邮件

mail [ -s Subject ] [ -c Address(es) ] [ -dinNv ] Address

标志

-c Address(es) 指定要向其发送消息副本的用户列表。可以指定一个或多个地址。当指定多于一个地址时，地址列表必须在引号（""）内。
 -e 测试系统邮箱内是否有邮件。如果存在要读取的邮件，mail 实用程序不会写入任何东西并带一个成功返回码而退出。
 -f FileName 从指定的文件读取消息。如果没有指定文件操作数，那么从 mbox 读取消息。从读取消息退出时，未删除的消息会写到这个文件中。
 -F 将消息记录在以收件人命名的文件中。该名称是在邮件标题中 To: 行的地址的最前部分。如果设置则覆盖 record 变量。
 -H 仅写标题摘要。
 -i 忽略 tty 中断信号。
 -n 禁止读取 /usr/share/lib/Mail.rc 文件。
 -N 禁止标题的初始打印。
 -s Subject 指定将创建的消息的主题。
 -u UserID 指定与执行 mail -f /var/spool/mail/UserID 等同的缩写。为指定的用户的邮箱启动 Mail 程序。必须有指定邮箱的访问许可权。
 -v 将 Mail 程序置为详细方式。显示传送到用户终端的详细信息。

例如：
 mail user@tom.com，mail -s "test" user2, mail user3

uencode $attachement1 $attachement2 | mailx -s "test" user@abc.com

邮件正文来源于letter.txt，邮件标题为&ldquo;test&rdquo;，把attch.jpg做为附件：
 mailx -s "test" " user@abc.com&lt;&lt; EOF &gt;  $(cat letter.txt)
 &gt; $(uuencode attch.jpg)
 &gt; EOF

uuencode 编码后的资料都以 begin 开始，以 end 作为结束。
 使用方式：uuencode [-hv] [sourcefile] targetfile
 参数：
 h 列出指令使用格式 (help)
 v 列出版本讯息 (version)
