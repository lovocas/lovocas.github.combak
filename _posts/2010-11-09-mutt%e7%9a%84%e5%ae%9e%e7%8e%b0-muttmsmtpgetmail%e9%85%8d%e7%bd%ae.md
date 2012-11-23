--- 
date: 2010-11-09 16:54:00 +08:00
layout: post
title: !binary |
    bXV0dOeahOWunueOsCwgbXV0dCttc210cCtnZXRtYWls6YWN572u

wordpress_id: 518
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NTE4

---
为什么是mutt？ 为什么mutt需要那么多其他工具才能完成邮件处理？你应该看看王垠的介绍：
 [url]http://www.kreny.com/docs/mutt.htm[/url]
 本文只杂乱谈一下我的配置经验
 先说说~/.muttrc几个基本问题，
 1.建立邮箱，mutt的邮箱有两种，一种是单文件（所有邮件放到一个文件里），另一种是mail dir形式，后一种目录形式比较好，推荐使用。

## folder &amp; spool
 set spoolfile = ~/Mail/inbox
 set folder    = ~/Mail
 set tmpdir    = ~/.mutt/tmp

set mbox_type = Maildir
 set mbox      = +inbox
 set record    = +sent
 set postponed = +postponed

mailboxes
 $MAIL
 =inbox
 =junkmail
 =favorites
 =archive

在mailboxes行里，可以定义自己想建立的邮箱，如favorites, archive, junkmail等。

2.编码问题

# locale
 set charset = UTF-8
 set locale  = zh_CN.UTF-8
 # hook charset
 charset-hook !UTF-8 gbk
 charset-hook "^us-ascii$" "utf-8"
 # send charset
 set send_charset="us-ascii:iso-8859-1:utf-8:gbk"
 set rfc2047_parameters=yes

charset-hook里设置里非utf-8邮件使用gbk查看

3.增加垃圾箱，虽然不是很完善
 #trash
 folder-hook . 'macro index d "s$folder/trashr"'
 folder-hook $folder/trash 'bind index d delete-message'

4.排序
 #sort
 set sort = reverse-date
 set sort_aux = threads

5.快捷键，
 我是这样设置的
 #key binding getmail
 macro index    G "!~/.getmail/getmailn"   "Invoke getmail"
 macro pager    G "!~/.getmail/getmailn"   "Invoke getmail"

#enter mailbox
 macro index  A  "c=archiver"
 macro pager  A  "c=archiver"
 macro index  I  "c!n" "Go to Inbox"
 macro pager  I  "c!n" "Go to Inbox"
 macro index  F    "c=favoritesr"
 macro pager  F    "c=favoritesr"
 macro index  J  "c=junkmailr"
 macro pager  J  "c=junkmailr"

前面一部分是收邮件（getmail）,快捷键是shift+g，（参见下面getmail配置）
 后面一部分是进入各个邮箱的快捷键。

6.多账号发送问题，方法是通过判断来信邮件头来选择，局限是只能在回信时自动选择。
 以下是完整的发信部分设置
 #send hook
 send-hook . 'my_hdr from:dream7180@gmail.com'
 send-hook . 'set sendmail="/usr/bin/msmtp"'
 reply-hook . 'my_hdr reply-to:dream7180@gmail.com'

send-hook '~t .*@163.com$' 'my_hdr from:dreamawake@163.com'
 send-hook '~t .*@163.com$' 'my_hdr reply-to:dreamawake@163.com'
 send-hook '~t .*@163.com$' 'set sendmail="/usr/bin/msmtp -a 163"'

reply-hook '~t .*@163.com$' 'my_hdr from:dreamawake@163.com'
 reply-hook '~t .*@163.com$' 'my_hdr reply-to:dreamawake@163.com'
 reply-hook '~t .*@163.com$' 'set sendmail="/usr/bin/msmtp -a 163"'

前面一段是默认的账号，后面两段是用163账号来发送（后面提到msmtp的配置）。

msmtp的配置：
 defaults
 tls on
 auth on
 tls_certcheck off
 logfile ~/.msmtplog

# Gmail service
 account gmail
 host smtp.gmail.com
 from dream7180@gmail.com
 port 587
 user dream7180@gmail.com
 password xxxxxx

# 163 service
 account 163
 tls off
 auth plain
 host smtp.163.com
 from dreamawake@163.com
 user dreamawake@163.com
 password xxxxxx

# Set a default account
 account default : gmail
 这里就定义了发送的账号，包括gmail和163

getmail的配置
 对多账号来说，getmail比fetchmail配置要复杂，但感觉收邮件的表现getmail比fetchmail要好。
 多账号配置，首先建立~/.getmail目录，编写~/.getmail/getmailrc.163文件：
 [retriever]
 type = SimplePOP3Retriever
 server = pop.163.com
 username = dreamawake@163.com
 password = xxxxxx

[destination]
 type = Maildir
 path = ~/Mail/inbox/

[options]
 verbose = 0
 delete = false
 message_log = ~/.getmail/getmail.log

诸如此类，编写~/.getmail/getmailrc.gmail文件

[retriever]
 type = SimplePOP3SSLRetriever
 server = pop.gmail.com
 port = 995
 username = dream7180@gmail.com
 password = xxxxxx

[destination]
 type = Maildir
 path = ~/Mail/inbox/

[options]
 verbose = 0
 delete = false
 message_log = ~/.getmail/getmail.log

你可以写更多.....

然后写一个收邮件的脚本文件~/.getmail/getmail
 #!/bin/bash
 #getmail for dream
 getmail -v -n -r ~/.getmail/getmailrc.sina -r ~/.getmail/getmailrc.163 -r ~/.getmail/getmailrc.gmail
 注意把它设为可运行（+x)。
 于是有了上面.muttrc里快捷键收邮件那部分配置。

附上我完整的~/.muttrc以供参考
 #号开头是注释

## user
 set realname = "rzli"

## folder &amp; spool
 set spoolfile = ~/Mail/inbox
 set folder    = ~/Mail
 set tmpdir    = ~/.mutt/tmp

set mbox_type = Maildir
 set mbox      = +inbox
 set record    = +sent
 set postponed = +postponed

mailboxes
 $MAIL
 =inbox
 =junkmail
 =favorites
 =archive

set move = no

# locale
 set charset = UTF-8
 set locale  = zh_CN.UTF-8
 # hook charset
 charset-hook !UTF-8 gbk
 charset-hook "^us-ascii$" "utf-8"
 # send charset
 set send_charset="us-ascii:iso-8859-1:utf-8:gbk"
 set rfc2047_parameters=yes

#Flag
 #  " "(空): 表示这封邮件不是给你的，也就是说 To: 和 Cc: Bcc: 都没有你的地址，
 #     很多转发的邮件，未确认的邮件列表邮件，垃圾邮件都有这个特征。
 # +: 表示你是收件人(To: 是你的地址之一)，而且是唯一的收件人。
 # T: 表示你是收件人(To: 包括了你的地址之一)，但是你不是唯一的收件人。这是一封群体信件。
 # C: 表示你的地址出现在 CC:，但是你不是唯一的被抄送的人。
 # F: 表示这封邮件是你发出去的。
 # L: 表示这是一封你已经加入的邮件列表寄来的。

#trash
 folder-hook . 'macro index d "s$folder/trashr"'
 folder-hook $folder/trash 'bind index d delete-message'

# Set header
 ignore headers *
 unignore headers from: to: cc: subject date
 set header=no

# set index
 set index_format="%4C %Z %{%y年%b%d}   %-15.15L (%?l?%4l&amp;%4c?) %s"

#score
 #score "~N" +4    #新信件
 #score "~D" -5    #有删除标记
 #score "~O" +1    #上次没有读
 #score "~f huiqin" +2    #来自 xxxx
 #score "~s xxxx" + 3    #主题上有 xxxx

#sort
 set sort = reverse-date
 set sort_aux = threads

#confirm
 set confirmappend = no

# wrap &amp; stop @ last mail
 set pager_stop = yes
 set smart_wrap = yes
 set pager_index_lines = 5

# message editor
 set editor = "vim"
 set fast_reply = yes
 set edit_headers = yes

# ext app to view attachment
 set mailcap_path="~/.mutt/mailcap"

# wait too see output info while quit
 set wait_key=yes

#Alias
 set alias_file = ~/.mutt/alias
 source ~/.mutt/alias

# Attachment
 set attach_format = "%u%D%I %t%4n %T%.40d%&gt; [%.7m/%.10M, %.6e%?C?, %C?, %s] "
 set attach_sep = "n"
 set attach_split = yes

# Color
 color hdrdefault black        cyan
 color quoted     red          white
 color signature  brightblack  white
 color indicator  brightwhite  magenta
 color attachment black        green
 color error      red          white
 color message    blue         white
 color search     brightwhite  magenta
 color status     brightyellow blue
 color tree       red          white
 color normal     blue         white
 color tilde      green        white
 color bold       brightyellow white
 color markers    red          white
 color index  brightwhite    red ~N
 color index  red    default ~O
 color index  brightblack    default ~D
 mono bold      bold
 mono underline underline
 mono indicator reverse

#
 auto_view text/html

# -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=:-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
 # include original content when reply
 set include = yes

# from
 set envelope_from = yes
 set encode_from = yes

#send hook
 send-hook . 'my_hdr from:dream7180@gmail.com'
 send-hook . 'set sendmail="/usr/bin/msmtp"'
 reply-hook . 'my_hdr reply-to:dream7180@gmail.com'

send-hook '~t .*@163.com$' 'my_hdr from:dreamawake@163.com'
 send-hook '~t .*@163.com$' 'my_hdr reply-to:dreamawake@163.com'
 send-hook '~t .*@163.com$' 'set sendmail="/usr/bin/msmtp -a 163"'

reply-hook '~t .*@163.com$' 'my_hdr from:dreamawake@163.com'
 reply-hook '~t .*@163.com$' 'my_hdr reply-to:dreamawake@163.com'
 reply-hook '~t .*@163.com$' 'set sendmail="/usr/bin/msmtp -a 163"'

#key binding getmail
 macro index    G "!~/.getmail/getmailn"   "Invoke getmail"
 macro pager    G "!~/.getmail/getmailn"   "Invoke getmail"

#enter mailbox
 macro index  A  "c=archiver"
 macro pager  A  "c=archiver"
 macro index  I  "c!n" "Go to Inbox"
 macro pager  I  "c!n" "Go to Inbox"
 macro index  F    "c=favoritesr"
 macro pager  F    "c=favoritesr"
 macro index  J  "c=junkmailr"
 macro pager  J  "c=junkmailr"

#End of muttrc, but it could be more and more and more:)
