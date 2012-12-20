---
layout: post
title: 使用ssmtp通过gmail发送邮件
tags:
- 系统管理
---
在Linux平台上有很多种发送邮件的方法，可以安装各种邮件服务程序。对于我们技术支持人员，常常需要把系统的一些故障告警，日志文件通过邮件发送给自己。可以安装部署sendmail， postfix，exim 等完整的邮件系统，不过也有一些不便：

* 完整的邮件系统配置相对复杂，而我只需要实现非常简单的监控邮件发送，有些杀鸡用牛刀了。
* 很多大型邮件系统，如新浪，会做IP地址反向DNS解析，会拒绝没有动态IP地址发送出来的邮件。

实际上，我要实现的目的就是发送告警邮件，并且希望减轻自己服务器的负担。那么，不如在服务器上设置邮件客户端，通过gmail这样稳定的邮件服务系统来发送邮件。
以下就是在gentoo平台快速设置ssmtp程序，通过gmail系统发送邮件的方法。当然，支持smtp发信认证，否则也不能通过gmail发送邮件了。

参考

Gmail and sSMTP
安装

ssmtp必须使用 mailwrapper 的USE参数来安装
{% highlight 'bash' %}
echo "mail-mta/ssmtp mailwrapper" >> /etc/portage/package.use
emerge -av mail-mta/ssmtp
{% endhighlight %}

#### 配置

##### 编辑 /etc/ssmtp/ssmtp.conf

{% highlight 'text' %}
root=youraccount@gmail.com
mailhub=smtp.gmail.com:465
rewriteDomain=
hostname=smtp.gmail.com:465
UseTLS=YES
AuthUser=youraccount
AuthPass=yourpasword
FromLineOverride=YES # optional
{% endhighlight %}

##### 编辑/etc/ssmtp/revaliases

	root:youraccount@gmail.com:smtp.gmail.com:465
	mainuser:youraccount@gmail.com:smtp.gmail.com:465
#### 使用

##### 安装mailx程序测试设置

    emerge mailx

##### 测试

    mail abc@example.com

此时会提示Subject:，输入主题后，主题和正文之间空一行。
正文输入完毕后，按下 Ctrl-D 表示正文结束。
ssmtp会提示 cc: ，此时可以输入需要抄送的邮件地址。抄送地址输入完毕后，再次按下 Ctrl-D 。
邮件则由ssmtp程序通过gmail认证发送，可以从gmail邮箱的已发送邮件中找到刚才发送的邮件。
排错

ssmtp投递失败的邮件将存放在 ~/dead.letter，可以通过以下命令重新发送：

    cat ~/dead.letter | sendmail user@domain.tld

检查 /var/log/messages 可以获取更多投递信息。类似

    Jul 30 09:56:33 x-box sSMTP[10830]: Creating SSL connection to host
    Jul 30 09:56:34 x-box sSMTP[10830]: SSL connection using RC4-MD5
    Jul 30 09:56:41 x-box sSMTP[10830]: Sent mail for xxxxxx@gmail.com (221 2.0.0 closing connection j28sm1929018qck.47) uid=0 username=root outbytes=420

邮件接受人看到的邮件中发送人地址信息为 root 。
