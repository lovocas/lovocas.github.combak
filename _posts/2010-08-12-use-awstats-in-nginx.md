--- 
date: 2010-08-12 18:19:22 +08:00
layout: post
title: 用awstats分析nginx日志
categories: 系统管理
tags: [nginx, awstats]
---
运行需要PERL的支持，因为Nginx对PERL支持很弱，只能通过生成Html方式来输出统计。

首先，需要每日切割日志文件。提供个脚本给大家

mv /usr/local/nginx/www.bun5.com.log /usr/local/nginx/logs/access5dft_`date +%Y%m%d`.log

killall –s USR1 nginx #使用USR1参数通知Nginx进程切换日志文件

很简单，两行实现切割。别问我格式，我网上抄来的。太困了，争取最快时间写完。

然后到某开源网wget回来，解压即可。我放到/usr/local/里面了，别问为什么，我习惯。

接下来的内容非常非常重要，大家睁大眼睛看清楚。修改日志配置为以下格式（具体位置在/usr/local/nginx/conf/你的网 址.conf）：

log_format  www.bun5.com  '$remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';

行了格式就是上面那样，要问为什么？因为默认格式Awstats不认。o(╯□╰)o

在刚刚解压回来的文件里面的tools找到awstats_configure.pl并执行开始配置。

-----> Check for web server install
Enter full config file path of your Web server.
Example: /etc/httpd/httpd.conf
Example: /usr/local/apache2/conf/httpd.conf
Example: c:Program filesapache groupapacheconfhttpd.conf
Config file path ('none' to skip web server setup):
#> none  #因为我们这里用的是 Nginx，所以写 none，跳过。
回车
Your web server config file(s) could not be found.
You will need to setup your web server manually to declare AWStats
script as a CGI, if you want to build reports dynamically.
See AWStats setup documentation (file docs/index.html)
-----> Update model config file '/usr/local/awstats/wwwroot/cgi-bin/awstats.model.conf'
File awstats.model.conf updated.
-----> Need to create a new config file ?
Do you want me to build a new AWStats config/profile
file (required if first install) [y/N] ?
#> y        #y 创建一个新的统计配置
回 车
-----> Define config file name to create
What is the name of your web site or profile analysis ?
Example: www.mysite.com
Example: demo
Your web site, virtual server or profile name:
#> www.5dft.com                #统计网站的域名
回车
-----> Define config file path
In which directory do you plan to store your config file(s) ?
Default: /etc/awstats
Directory path to store config file(s) (Enter for default):
#>
使用默认直接回车，接下来便会出现以下的提示
----> Add update process inside a scheduler
Sorry, configure.pl does not support automatic add to cron yet.
You can do it manually by adding the following command to your cron:
/usr/local/awstats/wwwroot/cgi-bin/awstats.pl -update -config=www.5dft.com
#回头把该命令填入crontab 按指定时间执行
Or if you have several config files and prefer having only one command:
/usr/local/awstats/tools/awstats_updateall.pl now
Press ENTER to continue...                回车继续

【上面从别的地方摘来的，别问从哪摘，N个网站都这篇文章我哪知道出处？反正我是上面少了一个步骤，少了个回车，其它正常。继续】

接下来就生成了个文件了。我的是在/etc/awstats/awstats.www.5dft.com.conf

在那个文件找到统计的日志文件的路径
LogFile="/var/log/httpd/mylog.log"
改为
LogFile="/opt/nginx/logs/access5dft_%YYYY-0%MM-0%DD-0.log

对应上边 Nginx 日志切割程序的所生成的目录存放结构，要注意 Awstats 的年月日格式的跟 Nginx 的写法有所不同。我们现在执行统计的顺序是：
Nginx 产生日志 –> 日志切割 –> Nginx 继续产生日志 –> 另存切割日志 –> 交由Awstats统计 –> 生成结果
在本文中 Awstats 所统计的日志，是已切下来的那部分。也能调转顺序，先统计完了再切。不过这比较容易造成统计的遗漏。配置修改完成后，保存退出。然后我们可以开始试一下手 动执行。

先执行日志切割脚本 logcron.sh 把 Nginx 的日志切下来。
然后执行 Awstats 日志更新程序开始统计分析。
./logcron.sh

/usr/local/awstats/wwwroot/cgi-bin/awstats.pl -update -config=www.5dft.com

看到几行类似

Found 0 dropped records,
Found 0 corrupted records,
Found 0 old records,
Found 120 new qualified records.

就是成功了。如果没有的话是缺少一个/var/lib什么什么的，mkdir一下就可以。我发现网上教程都少了这一步，耽误小白啊。o(╯□╰)o 还好我是大白，很快解决。继续。

那么我们就必须建个文件夹来放文件咯。放在网站里面方便看。
省略建文件夹步骤，继续。

/usr/local/awstats/tools/awstats_buildstaticpages.pl -update -config=www.bun5.com -lang=cn -dir=/home/wwwroot/oc86.com/xxx -awstatsprog=/usr/local/awstats/wwwroot/cgi-bin/awstats.pl

上面的命令我不查资料还真不知道。awstats_buildstaticpages.pl是生成静态页面的。后面指定更新，后面语言CN，后面指 定生成静态的目录，后面awstats.pl是Awstats 日志更新程序路径。

请注意上面的命令是一行的，切勿分割或者别的什么的。
把那个和分割日志的放进crontab即可完成任务。

好了，到此任务基本完成。剩下的生成的文件要怎么加密就是你的问题了。我是懒虫，试了几次500之后我就直接改个复杂的文件夹名就完事了。但是别塞 进robot，否则...嘿嘿，别人一看就知道了。
