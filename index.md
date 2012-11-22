---
layout: default
title: 新的个人日志
tagline: 尝试着安装了Jekyll Bootstrap
---
{% include JB/setup %}

## 安装新的Blog

之前使用过跟这个程序类似的octpress管理日志，但是因为要在自己的服务器搭建环境，用起来稍
有些麻烦，总觉得用起来没那么流畅，这次装上Jekyll Bootstrap，至少到现在为止用起来的感觉
还不错，希望真的可以长时间用下去，也希望[Github](http://www.github.com)的Pages服务不要停。
    
## 日志列表

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

