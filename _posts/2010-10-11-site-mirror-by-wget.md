--- 
date: 2010-10-11 12:25:04 +08:00
layout: post
title: !binary |
    55Sod2dldOWBmuermeeCuemVnOWDjw==

wordpress_id: 476
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDc2

---
# wget -r -p -np -k http://xxx.edu.cn

-r 表示递归下载,会下载所有的链接,不过要注意的是,不要单独使用这个参数,因为如果你要下载的网站也有别的网站的链接,wget也会把别的网站的东西下载下来,所以要加上-np这个参数,表示不下载别的站点的链接.
-np 表示不下载别的站点的链接.
-k 表示将下载的网页里的链接修改为本地链接.
-p 获得所有显示网页所需的元素,比如图片什么的.
