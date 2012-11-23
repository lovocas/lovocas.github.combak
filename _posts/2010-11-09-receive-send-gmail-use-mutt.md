--- 
date: 2010-11-09 08:58:00 +08:00
layout: post
title: !binary |
    55SobXV0dOaUtuWPkUdtYWls6YKu5Lu2

wordpress_id: 516
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NTE0

---
首先当然是你的Gmail帐户中的POP3设置要允许pop3接收邮件

1.POP3收取Gmail邮件的关键在于SSL安全验证，需要安装如下软件包
 代码:
 sudo apt-get install ca-certificates libssl0.9.8 openssl ssl-cert

2,单帐户的话可以直接用mutt收取邮件，只需在.muttrc中增加如下行
 引用:
 set pop_user=usr@gmail.com
 set pop_pass="password"
 set pop_host=pops://pop.gmail.com
 set pop_last=yes

3.安装mutt时默认安装Postfix（send mail服务器）,不过我用msmtp来发邮件
 只需在.muttrc中指明send mail用的是msmtp:
 引用:
 set sendmail="/usr/bin/msmtp"
 set use_from=yes
 set from= usr@gmail.com
 set envelope_from=yes

4.我的.msmtprc配置文件,注意修改权限为0600


 引用:
 defaults
 tls on

#Gmail

account gmail
 host smtp.gmail.com
 from usr@gmail.com
 auth on
 user usr@gmail.com
 password password
 port 587

account default : gmail

5,我的.muttrc配置文件

========================

引用:
 set editor="vim" #设置默认编辑器
 #建立信箱
 set mbox="~/Mail/inbox"
 set mbox_type=maildir
 set spoolfile="~/Mail/inbox"
 set postponed="~/Mail/postponed"
 set record="~/Mail/sent"

#关于msmtp的设置
 set sendmail="/usr/bin/msmtp"
 set use_from=yes
 set from=usr@gmail.com
 set envelope_from=yes

#set alternates=" usr@gmail.com"
 # 这是一个格式字符串，用来控制你的index的列表显示。它的缺省定义是
 # set index_format="%4C %Z %{%b %d} %-15.15L (%4l) %s"
 #指定你有哪 些信箱文件。当你按 "c" 切换信箱时，再按 Tab 键，这些信箱就可供你选择
 mailboxes Mailbox sent-mail
 # 这是一个 bool 型变量。它表明你在回信时引用原文是否加入原文的邮件头。
 set header=no
 # 可以设置为 yes, no, ask-yes, 或者 ask-no.
 # 这是说，当你按q退出时，是否提示你(ask-yes,ask-no)，
 # 还是直接就退出了(yes)，还是根本不理你(no)。
 set quit=ask-yes
 # 那些类型的附件是允许直接通过 .mailcap 浏览的？
 auto_view text/html
 # 这也是一个提示性变量。它是确定当你退出时，是否提示你把信件
 #从spool 移动到的mbox 文件。
 set move=no
 # 当 Mutt 用 thread 方式显示时，是否用纯 ascii 表示树状列表。
 set ascii_chars=yes
 # 回信时是否包含原文。
 set include
 # 回信的引文之前插入那个符号？
 set indent_str="&gt; "
 # 设置你自己的邮件头。
 my_hdr From: usr@gmail.com
 # 打分
 # 新信件+4分,主题包含"通知"的+2，主题包含 "Circulation" +3, 已经标记
 # 删除的 -5，上次没有读的 +1，包含 "believe"的 -10(垃圾广告！)。
 score "~N" +4
 score "~s 通知" +2
 score "~s Circulation" +3
 score "~D" -5
 score "~O" +1
 score "~s believe" -10
 # 排序方式。
 set sort=score
 # 当用 thread 排序方式时，我们对各个 thread 的相对排序顺序。
 set sort_aux=date
 # 如果翻到最后，再按往下翻也不会翻到下一封信件
 set pager_stop
 # 如果设置，当你按 "r"
 # 回信时，就不会再提示你输入回信地址和主题，直接进入编辑
 #模式。
 set fast_reply
 # 当你按 "t" 或者 "D" 之类的标记操作时，是否自动把光标移动到下一封信件。
 set resolve=yes
 # 地址簿
 source ~/.mutt.alias
 # 当你在 index 里按 "a"
 # 为来信者取别名时，使用哪一个别名文件保存这个别名。
 set alias_file=/home/lixuebai/.mutt.alias
 # 你发出的邮件保存到那个信箱文件？比如可以像我这样每个月发出的信件放在不同的文件
 里。
 set record="~/Mail/=sent-mail-`date +%Y-%m`"
 # 你的终端支持哪一种编码的显示？这个必须和你的终端编码一样。
 #注意这个如果设置不正确的话会导致在写邮件时出现一个"bad IDN"的错误信息
 set charset="utf-8"
 # send_charset
 set send_charset="gb2312:utf-8:us-ascii:iso-8859-1"
 # 外部程序退出时，是否要求用户按一个键才返回。这在察看某些shell命令输出时是比要&gt;的，
 # 否则它们一下就消失了。
 set wait_key=yes
 # 当你要把信件存到另一个信箱，而这个信箱已经存在时，是否提示附加？
 set noconfirmappend
 # 是否把邮件头也放在编辑器里可以修改？
 set edit_headers=no
 # 当你在用 pager 看信件时，在 index 留出多少行显示邮件列表？
 set pager_index_lines=4
 # 告诉 Mutt 你已经订阅了那些邮件列表(mailing-list).
 # subscribe fvwm@fvwm.org

# mutt显示日期为中文
 set locale="zh_CN"
 # 有些没有设置字符编码时
 #charset-hook .* gb2312
 # Chinaren 等服务器发出来的信件使用了 quoted-printable 的 subject，
 # 而且设置编码为 "iso8859-1"，这显然是错误的。
 # 对付这个错误的办法是把 iso-8859-1 变成 gb2312 的别名
 charset-hook ^iso-8859-1$ gb2312
 # evolution 发过来的 subject 为 utf-8 编码的邮件标题乱码！
 # 那就把不是 utf-8 的编码都映射到 gb2312
 charset-hook !utf-8 gb2312
 # mutt进行pop3收信，当然之前要查看$ mutt -v 编辑情况的确编辑了pop3了。
 # 以后进入mutt按G就可以收信了。
 set pop_user=usr@gmail.com
 set pop_pass="password"
 set pop_host=pops://pop.gmail.com
 set pop_last=yes

#颜色设置
 color normal brightblue default
 color attachment blue default
 color error red white
 color status white blue
 color hdrdefault magenta default
 color indicator black cyan

=========================================
