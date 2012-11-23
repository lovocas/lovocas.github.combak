--- 
date: 2010-11-09 10:42:00 +08:00
layout: post
title: !binary |
    6YWN572u5oiQ5YqfZXhpbTQrZmV0Y2htYWwrbXV0dOaUtuWPlmdtYWls

wordpress_id: 517
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NTE2

---
目标：Debian环境下，用Exim4 + fetchmail + mutt来收发gmail电子邮件。

1、设置exim4，通过smarthost外发邮件

$dpkg-reconfigure exim4-config

(1)将配置文档拆分成小文件么？ 否
 (2)选择&ldquo;用smarthost发信；通过smtp或fetchmail接受邮件&rdquo;
 (3)系统邮件名称，随意；（这里我写了tom.com)
 (4)要监听的入站 SMTP 连接的 IP 地址：127.0.0.1 (这样，就只有本机可以利用exim4)
 (5)其它可接受的邮件目的地址：(empty)
 (6)为这些主机进行邮件转发：(empty)
 (7)负责处理从本机寄出的邮件的机器(smarthost)：真正用来外发邮件的smtp地址
 （我这里用smtp.gmail.com)
 (8)要在寄出的邮件中隐藏本地邮件名称吗？ 是
 (9)本地用户的可视域名：同（3）
 (10)保持最小 DNS 查询量吗(按需拔号 Dial-on-Demand)？ 否

还要修改几个配置文件：
 (1)/etc/exim4/passwd.client，smtp的帐号密码设置，加入：

smtp.gmail.com:someone@gmail.com:password
 gmail-smtp.l.google.com:someone@gmail.com:password

后一个地址非常重要，当 exim 联向 smtp.gmail.com 时它会以类似重定向的方式联向 gmail-smtp.l.google.com，所以也要给它写上认证信息。

(2)/etc/email-addresses，系统邮箱地址，加入：

user: josephpei@gmail.com

(3)/etc/exim4/exim4.conf.template，exim4配置文件

### transport/30_exim4-config_remote_smtp_smarthost
 #################################

# This transport is used for delivering messages over SMTP connections
 # to a smarthost. The local host tries to authenticate and does some
 # modification in headers and return-path.
 # This transport is used for smarthost and satellite configurations.

remote_smtp_smarthost:
 debug_print = "T: remote_smtp_smarthost for $local_part@$domain"
 driver = smtp
 # hosts_try_auth = ${if exists {CONFDIR/passwd.client}{DCsmarthost}{}}
 tls_tempfail_tryclear = false
 # DEBCONFheaders_rewriteDEBCONF
 # DEBCONFreturn_pathDEBCONF
 hosts_require_auth = smtp.gmail.com
 hosts_require_tls = smtp.gmail.com
 port=587

配置完以后，update-exim4.conf，update-exim4.conf.template，然后重启 exim4 服务即可。

2、配置mutt：编辑/etc/Muttrc
 这里加入：

set envelope_from=yes
 set sendmail="/usr/sbin/exim4"
 set from=josephpei@gmail.com
 set realname="josephpei"
 set use_from=yes

3、opensll认证:
 (1)取得gmail的cert文件
 运行:

$openssl s_client -connect smtp.gmail.com:995 -showcerts

输出:
 CONNECTED(00000003)
 depth=0 /C=US/ST=California/L=Mountain View/O=Google Inc./CN=pop.gmail.com
 verify error:num=20:unable to get local issuer certificate
 verify return:1
 depth=0 /C=US/ST=California/L=Mountain View/O=Google Inc./CN=pop.gmail.com
 verify error:num=27:certificate not trusted
 verify return:1
 depth=0 /C=US/ST=California/L=Mountain View/O=Google Inc./CN=pop.gmail.com
 verify error:num=21:unable to verify the first certificate
 verify return:1
 ---
 Certificate chain
 0 s:/C=US/ST=California/L=Mountain View/O=Google Inc./CN=pop.gmail.com
 i:/C=US/O=Equifax/OU=Equifax Secure Certificate Authority
 -----BEGIN CERTIFICATE-----
 MIIC3TCCAkagAwIBAgIDBZIAMA0GCSqGSIb3DQEBBQUAME4xCzAJBgNVBAYTAlVT
 MRAwDgYDVQQKEwdFcXVpZmF4MS0wKwYDVQQLEyRFcXVpZmF4IFNlY3VyZSBDZXJ0
 aWZpY2F0ZSBBdXRob3JpdHkwHhcNMDUxMTE1MjEyMjQ0WhcNMDcxMTE2MjEyMjQ0
 WjBoMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMN
 TW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xFjAUBgNVBAMTDXBv
 cC5nbWFpbC5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMP8LCYiLGJ/
 RihwcOi1V/zHVTw0Gfu+mI141Vjuuj2DtQoav8emwlXbu8gZoKP9GeMWpX1Vo9qN
 4gkslIToHmDnIwGjcaEAfpdhSR9g54Kf5Y7BEXVyco6mTIlpe9vsbV0dmB1FvLP2
 1N09dkUJfi7V0fjb8mcn3QYu6+6QNoxPAgMBAAGjga4wgaswDgYDVR0PAQH/BAQD
 AgTwMB0GA1UdDgQWBBTdASsopgao1m8hcEg0cDZhucltljA6BgNVHR8EMzAxMC+g
 LaArhilodHRwOi8vY3JsLmdlb3RydXN0LmNvbS9jcmxzL3NlY3VyZWNhLmNybDAf
 BgNVHSMEGDAWgBRI5mj5K9KylddH2CMgEE8zmJCf1DAdBgNVHSUEFjAUBggrBgEF
 BQcDAQYIKwYBBQUHAwIwDQYJKoZIhvcNAQEFBQADgYEAln3/pVqYnUXA1TVGzOqX
 LFhohGxpuNkr1UJnQmYxmZeB07uPBYRX8c0JXEKs29TmAHRsLhmp8kF36F11Dxgi
 Xm/Y8I9zgWHoMj7SL3Ve/u8K8K7XcUyUuaWmldLQAREafpFy+f+KYHGuAVh8hjy6
 XyPlMCqj+PNp8QXjgOcgO68=
 -----END CERTIFICATE-----
 ---
 Server certificate
 subject=/C=US/ST=California/L=Mountain View/O=Google Inc./CN=pop.gmail.com
 issuer=/C=US/O=Equifax/OU=Equifax Secure Certificate Authority
 ---
 No client certificate CA names sent
 ---
 SSL handshake has read 891 bytes and written 338 bytes
 ---
 New, TLSv1/SSLv3, Cipher is DES-CBC3-SHA
 Server public key is 1024 bit
 SSL-Session:
 Protocol : TLSv1
 Cipher : DES-CBC3-SHA
 Session-ID: CFCAB44667A90184C8ABCC6F4D2D1C8EC29A9DBDAD11D815E7E22DC5E34213F6
 Session-ID-ctx:
 Master-Key: 1AFCF4EC31DF0A5930B527BDC55B86D69285DD044E939BDDF18884F61F1E8340EFE7BF85CC50F98F657FB0579CF612F7
 Key-Arg : None
 Start Time: 1134601370
 Timeout : 300 (sec)
 Verify return code: 21 (unable to verify the first certificate)
 ---
 +OK Gpop m2pf1356431nzf ready.
 将中间的:
 -----BEGIN CERTIFICATE-----
 ......
 -----END CERTIFICATE-----
 拷贝到~/.certs/gmail.pem(自己建立这个目录和文件）

(2) 生成CA文件

$openssl x509 -in gmail.pem -noout -fingerprint

将上面的gmail.pem前面加上Fingerprint

MD5 Fingerprint: 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E1:1B:EC4
 PEM Data:
 -----BEGIN CERTIFICATE-----
 ......
 -----END CERTIFICATE-----

（gmail.pem似乎可以不加fingerprint，我加上，就没有删，就画个蛇加个足吧）
 在~/.certs/建立equifax.pem文件，内容如下：

MD5 Fingerprint: 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4
 PEM Data:
 -----BEGIN CERTIFICATE-----
 MIIDIDCCAomgAwIBAgIENd70zzANBgkqhkiG9w0BAQUFADBOMQswCQYDVQQGEwJV
 UzEQMA4GA1UEChMHRXF1aWZheDEtMCsGA1UECxMkRXF1aWZheCBTZWN1cmUgQ2Vy
 dGlmaWNhdGUgQXV0aG9yaXR5MB4XDTk4MDgyMjE2NDE1MVoXDTE4MDgyMjE2NDE1
 MVowTjELMAkGA1UEBhMCVVMxEDAOBgNVBAoTB0VxdWlmYXgxLTArBgNVBAsTJEVx
 dWlmYXggU2VjdXJlIENlcnRpZmljYXRlIEF1dGhvcml0eTCBnzANBgkqhkiG9w0B
 AQEFAAOBjQAwgYkCgYEAwV2xWGcIYu6gmi0fCG2RFGiYCh7+2gRvE4RiIcPRfM6f
 BeC4AfBONOziipUEZKzxa1NfBbPLZ4C/QgKO/t0BCezhABRP/PvwDN1Dulsr4R+A
 cJkVV5MW8Q+XarfCaCMczE1ZMKxRHjuvK9buY0V7xdlfUNLjUA86iOe/FP3gx7kC
 AwEAAaOCAQkwggEFMHAGA1UdHwRpMGcwZaBjoGGkXzBdMQswCQYDVQQGEwJVUzEQ
 MA4GA1UEChMHRXF1aWZheDEtMCsGA1UECxMkRXF1aWZheCBTZWN1cmUgQ2VydGlm
 aWNhdGUgQXV0aG9yaXR5MQ0wCwYDVQQDEwRDUkwxMBoGA1UdEAQTMBGBDzIwMTgw
 ODIyMTY0MTUxWjALBgNVHQ8EBAMCAQYwHwYDVR0jBBgwFoAUSOZo+SvSspXXR9gj
 IBBPM5iQn9QwHQYDVR0OBBYEFEjmaPkr0rKV10fYIyAQTzOYkJ/UMAwGA1UdEwQF
 MAMBAf8wGgYJKoZIhvZ9B0EABA0wCxsFVjMuMGMDAgbAMA0GCSqGSIb3DQEBBQUA
 A4GBAFjOKer89961zgK5F7WF0bnj4JXMJTENAKaSbn+2kmOeUJXRmm/kEd5jhW6Y
 7qj/WsjTVbJmcVfewCHrPSqnI0kBBIZCe/zuf6IWUrVnZ9NA2zsmWLIodz2uFHdh
 1voqZiegDfqnc1zqcPGUIWVEX/r87yloqaKHee9570+sB3c4
 -----END CERTIFICATE-----

(3)rehash上步两个文件：equifax.pem和gmail.pem，运行:

$ c_rehash .certs

(4)检查CA文件，运行:

$openssl s_client -connect pop.gmail.com:995 -CApath .certs/

如果输出中有:
 Verify return code: 0 (ok)
 则CA文件是好的.

4、配置fetchmail
 这个比较简单，修改~/.fetchmail文件，加入：

#
 #
 # Sample /home/chirico/.fetchmailrc file for Gmail
 #
 # Check mail every 90 seconds
 set daemon 90
 set syslog
 set postmaster chirico
 #set bouncemail
 #
 # Google Gmail  is mchirico but on computer it is chirico
 #  To keep mail on the server use the you would put keep at the end.
 # user 'mchirico@gmail.com' with pass "pa33w0r8"  is 'chirico' here options ssl sslcertck  sslcertpath '/home/chirico/certs/.certs' keep
 #
 poll pop.gmail.com with proto POP3 and options no dns
 user 'mchirico@gmail.com' with pass "pa33w0r8"  is 'chirico' here options ssl sslcertck  sslcertpath '/home/chirico/certs/.certs'
 smtphost localhost
 # You would use this to by-pass Postfix
 # mda '/usr/bin/procmail -d %T'

tsinghua brep：如果有重复收信问题
 poll pop3.sina.com.cn with proto POP3 uidl
 ~~~~加上这个
 然后执行：

$chmod 0600 .fetchmailrc

呵呵，终于完工了，测试：

josephpei@hit-pei:~$ fetchmail -avk
 fetchmail: 6.3.2 querying pop.gmail.com (protocol POP3) at 2006年04月06日 星期四 20时07分26秒: poll started
 fetchmail: Issuer Organization: Equifax
 fetchmail: Unknown Issuer CommonName
 fetchmail: Server CommonName: pop.gmail.com
 fetchmail: pop.gmail.com key fingerprint: 59:51:61:89:CD:DD:B2:35:94:BB:44:97:A0:39:D5:B4
 fetchmail: POP3&lt; +OK Gpop ready z52pf972624pyg fetchmail: POP3&gt; CAPA
 fetchmail: POP3&lt; +OK Capability list follows
 fetchmail: POP3&lt; USER
 fetchmail: POP3&lt; RESP-CODES
 fetchmail: POP3&lt; EXPIRE 0
 fetchmail: POP3&lt; LOGIN-DELAY 300
 fetchmail: POP3&lt; X-GOOGLE-VERHOEVEN
 fetchmail: POP3&lt; . fetchmail: POP3&gt; USER josephpei@gmail.com
 fetchmail: POP3&lt; +OK send PASS fetchmail: POP3&gt; PASS *
 fetchmail: POP3&lt; +OK Welcome. fetchmail: POP3&gt; STAT
 fetchmail: POP3&lt; +OK 1 2305 1 message for josephpei@gmail.com at pop.gmail.com (2305 octets). fetchmail: POP3&gt; LIST 1
 fetchmail: POP3&lt; +OK 1 2305 fetchmail: POP3&gt; RETR 1
 fetchmail: POP3&lt; +OK message follows
 reading message josephpei@gmail.com@pop.gmail.com:1 of 1 (2305 octets)
 fetchmail: SMTP&lt; 220 hit-pei.optic ESMTP Exim 4.60 Thu, 06 Apr 2006 20:07:31 +0800 fetchmail: SMTP&gt; EHLO hit-pei.optic
 fetchmail: SMTP&lt; 250-hit-pei.optic Hello localhost [127.0.0.1]
 fetchmail: SMTP&lt; 250-SIZE 52428800
 fetchmail: SMTP&lt; 250-PIPELINING
 fetchmail: SMTP&lt; 250 HELP fetchmail: SMTP&gt; MAIL FROM: SIZE=2305
 fetchmail: SMTP&lt; 250 OK fetchmail: SMTP&gt; RCPT TO:
 fetchmail: SMTP&lt; 250 Accepted fetchmail: SMTP&gt; DATA
 fetchmail: SMTP&lt; 354 Enter message, ending with "." on a line by itself #************************.****fetchmail: SMTP&gt;. (EOM)
 fetchmail: SMTP&lt; 250 OK id=1FRTGt-00015t-5g  not flushed fetchmail: POP3&gt; QUIT
 fetchmail: POP3&lt; +OK Farewell. fetchmail: SMTP&gt; QUIT
 fetchmail: SMTP&lt; 221 hit-pei.optic closing connection
 fetchmail: 6.3.2 querying pop.gmail.com (protocol POP3) at 2006年04月06日 星期四 20时07分31秒: poll completed
 fetchmail: normal termination, status 0
 josephpei@hit-pei:~$ mutt
 4 封信件被保留, 0 封信件被删除。

附（还未试）：为了和word附件的配合，需要在.muttrc中加入：
 auto_view application/msword
 另外，还需要在~/.mailcap中加入：
 text/html; lynx -force_html %s; needsterminal;
 application/msword; /usr/bin/antiword '%s'; copiousoutput; #这里用到antiword，apt-get install就行
 description="Microsoft Word Text"; nametemplate=%s.doc
 这样，就能够在mutt中显示word附件了。
