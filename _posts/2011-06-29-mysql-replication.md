---
layout: post
title: mysql主备部署
tags:
- mysql
- replication
- 数据库管理
type: post
---
本文说的mysql主备是指单向、异步的数据复制。可以是一个主、多个备。
这样做的好处显而易见：有利于健壮性、速度和系统管理。备用数据库可以做为只读查询和备份的机器，减轻主用数据库的负担。

实施前提：
主用数据库的配置里至少有：

{% highlight text %}
[mysqld]
log-bin=mysql-bin
server-id=1
{% endhighlight %}

建议主备的数据库版本一致。
我所知道的最简单的实施方式（不需要停主库，甚至不需要长时间地禁止主库写入）：

主库操作：
在主库里建一个复制用的用户：
{% highlight sql %}
grant replication slave on *.* TO 'replication'@'备库地址' identified by 'replication';
{% endhighlight %}
导出主库的数据，并记下当然日志文件和偏移：
{% highlight bash %}
mysqldump --master-data=2 --single-transaction -uroot -p --all-databases > dumpfile
{% endhighlight %}

这里是把数据以SQL的形式导出，并记下导出瞬间的日志文件和偏移(得益于–master-data=2参数)，出来的dumpfile的前面会有一行类似以下的注释信息，就是文件名和偏移值了：
{% highlight sql %}
CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000011', MASTER_LOG_POS=1061553673;
{% endhighlight %}
对于这步，网络上一般采用的方法为：
{% highlight sql %}
FLUSH TABLES WITH READ LOCK；--先把主库设置成只读，然后导出SQL或者直接复制数据文件
SHOW MASTER STATUS; --记下日志文件和偏移
UNLOCK TABLES; --恢复主库写入
{% endhighlight %}

这样，至少在复制数据的那段时间，主库是不可提供服务的。

备库操作：
复制主库的/etc/my.cnf和dumpfile。
把主库配置里的 server-id 改成2（或者3、4，多个备库保存互不相同），再加上
{% highlight text %}
relay-log = slave-relay.log
relay-log-index = slave-relay-log.index
{% endhighlight %}
导入数据：

mysql 进入mysql命令行执行：
{% highlight sql %}
SLAVE STOP;
CHANGE MASTER TO
MASTER_HOST='主库地址',
MASTER_PORT=3306,
MASTER_USER='replication',
MASTER_PASSWORD='密码',
MASTER_LOG_FILE='mysql-bin.000011',
MASTER_LOG_POS=1061553673;
SLAVE START;
{% endhighlight %}

然后，就搞定了，可以在备库执行
`SHOW PROCESSLIST;`
查看同步的状态，如果此命令的输出里有两个“system user”的进程，并且Command都是“Connect”的话，就差不多OK了，此时，任何对主库的修改，都能准实时地从备库里查询出

参考文章：[mysql官方中文手册](http://dev.mysql.com/doc/refman/5.1/zh/replication.html)
