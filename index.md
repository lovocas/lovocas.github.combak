---
layout: default
title: 首页 
tagline: 智者创造机会，强者把握机会，弱者等待机会～
---
{% include JB/setup %}

## 日志列表

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

