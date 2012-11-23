--- 
date: 2007-12-03 15:41:10 +08:00
layout: post
title: !binary |
    TXlTUUwgUHJveHkgLSBNeVNRTOS7o+eQhueahOS7i+e7jeWPiuS4quS6uuS9
    v+eUqOe7j+mqjCjovawp

wordpress_id: 51
wordpress_url: !binary |
    aHR0cDovL3d3dy5sdjgwLmNuL3dwLzIwMDcvMTIvMDMvbXlzcWwtcHJveHkt
    bXlzcWwlZTQlYmIlYTMlZTclOTAlODYlZTclOWElODQlZTQlYmIlOGIlZTcl
    YmIlOGQlZTUlOGYlOGElZTQlYjglYWElZTQlYmElYmElZTQlYmQlYmYlZTcl
    OTQlYTglZTclYmIlOGYlZTklYWElOGMlZTglYmQlYWMv

---
收到Mysql AB的邮件, 看到 Mysql Proxy 这个东西发布的时候,我很兴奋.
因为当时我在弄一个项目,需要网通和电信两个镜像站点同时读写一个布署在电信服务器上的mysql数据库.中间有台双线的服务器可以做数据中转,试过很多方案都不理想.

Mysql Proxy很好的解决了我的问题.虽然目前还是测试版,可到现在三个月了,我的这个业务一直很稳定的在运行着.



下面就介绍一下当时的情况:

目的很明确,在下面的图片中,我仅希望web server 3(网通线路)的服务器能快速读写Data base server中的数据.
<img />

但由于"<a>世界上最遥远的距离不是...而是中国电信到中国网通</a>" 这个中国特色的问题,我从web server 3到Data base server 的ping 值在夜间闲时都在300ms以上,所以,通过第一种走法(蓝色).基本上网通的镜像慢得等于没用.

还好,我有台双线服务器托管在双线机房,它可以比较快速的连接Data base server和web server 3. 但这台服务器在跑其它重要业务,只能做数据中转.

最后.我在web server 2 (双线路) 上安装了Mysql proxy. 当web server 3需要连接 Data base server 的数据库时,只需要连接双线服务器的网通IP(红线走法),就OK了.

那什么是Mysql proxy 呢?
<img />

MySQL代理是一个介于MySQL客户端和MySQL服务器之间的简单程序，可用来监视、分析或者传输他们之间的通讯。他的灵活性允许你最大限度的使用它，这些包括负载平衡、容错 、分析查询和修正等等。官方网址是:<a>http://forge.mysql.com/wiki/MySQL_Proxy</a>

我在<a>http://dev.mysql.com/downloads/mysql-proxy/</a>下载了linux系统平台下的 MySQL proxy alpha 编译好的二进制版本.

解压,然后复制到/usr/local/sbin下,就开始可以使用了.(如果下的是源码,,则需要make一下)
$ tar zxfmysql-proxy-0.5.0.tar.gz
$ cp ./mysql-proxy-0.5.0/sbin/mysql-proxy /usr/local/sbin

命令语法很简单,它默认使用4040端口.
假设你想代理本机的mysql,直接执行
$ mysql-proxy &amp;
就可以了.注意，mysql server必须是5.0或者更高版本.

然后测试一下
$ mysql --host=127.0.0.1 --port=4040 --user=root  --password
输入你的root密码,嘿嘿.和使用3306端口的一样.

有人会问:神经病吗?我直接用3306更直接和快速呀,为何多此一举,加个代理?
我能想到的是:
1.在大的应用时,有时需要将读写分离,提高效率,就会需要.
2.数据库做负载平衡(load balancing)时,也需要.
3.对local用户不需要复杂的安全验证,对远程用户需要ssl时.
4.其它一些特殊要求.
可惜,我所接触的项目没那么大,暂时不需要负载平衡(load balancing)和读写分离,只是个简单意义上的代理.于是我用参数--proxy-backend-addresses 让它代理Data base server这台服务器的3306端口
$ mysql-proxy   --proxy-backend-addresses=192.168.0.1:3306 &amp;

测试连接:
$ mysql --host=localhost --port=4040   --user=root --password
输入Data base server的密码.连接上了.

到网通服务器上测试,
$ mysql --host=双线服务器的网通IP地址 --port=4040   --user=root --password
输入密码后,立即就能看到mysql&gt;了.

套上PHP代码,测试. 速度非常好.目的达到了.

如果你也有类似或者 mysql server 负载平衡、容错 等的应用需求时,不妨也试试mysql-proxy,
相信不会让你失望的.
也欢迎和我探讨这方面的问题. 我的联系方式在<a>这里!!</a>
-------------------------------------------------------------------
附mysql-proxy 的所有语法命令:
$ mysql-proxy --help-all

Usage:

mysql-proxy [OPTION...] - MySQL Proxy

Help Options:

-?, --help                                          Show help options
--help-all                                          Show all help options
--help-admin                                        Show options for the admin-module
--help-proxy                                        Show options for the proxy-module

admin module

--admin-address=&lt;host:port&gt;listening address:port of internal admin-server (default: :4041)

proxy-module

--proxy-address=&lt;host:port&gt;listening address:port of the proxy-server (default: :4040)
--proxy-read-only-backend-addresses=&lt;host:port&gt;address:port of the remote slave-server (default: not set)
--proxy-backend-addresses=&lt;host:port&gt;address:port of the remote backend-servers (default: 127.0.0.1:3306)
--proxy-skip-profiling                              disables profiling of queries (default: enabled)
--proxy-fix-bug-25371                               fix bug #25371 (mysqld &gt;5.1.12) for older libmysql versions
--proxy-lua-script=&lt;file&gt;filename of the lua script (default: not set)
--no-proxy                                          Don't start proxy-server

Application Options:
-V, --version                                       Show version
--daemon                                            Start in daemon-mode
--pid-file=&lt;file&gt;PID file in case we are started as daemon
