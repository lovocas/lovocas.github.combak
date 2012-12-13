---
layout: post
title: Converting rewrite rules
tags:
- nginx
- rewrite
- 简单生活
status: publish
type: post
published: true
meta:
  views: '64'
  slider_hidetitle: 'off'
  slider_image: http://cntoby-wordpress.stor.sinaapp.com/uploads/2012/01/20110413022039414.gif
  _edit_last: '2'
---
A redirect to a main site
People who during their shared hosting life used to configure everything using only Apache’s .htaccess files, translate usually the following rules
{% highlight httpd %}
RewriteCond %{HTTP_HOST} nginx.org
RewriteRule (.*) http://www.nginx.org$1
{% endhighlight %}
in something like this:
{% highlight nginx %}
server {
    listen 80;
    server_name www.nginx.org nginx.org;
    if ($http_host = nginx.org) {
        rewrite (.*) http://www.nginx.org$1;
    }
    ...
}
{% endhighlight %}
This is a wrong, cumbersome, and ineffective way. The right way is to define a separate server for nginx.org:
{% highlight nginx %}
server {
    listen 80;
    server_name nginx.org;
    rewrite ^ http://www.nginx.org$request_uri?;
}

server {
    listen 80;
    server_name www.nginx.org;
    ...
}
{% endhighlight %}
Another example, instead of backward logic: all that is not nginx.com and is not www.nginx.com:
{% highlight httpd %}
RewriteCond %{HTTP>HOST} !nginx.com
RewriteCond %{HTTP_HOST} !www.nginx.com
RewriteRule (.*) http://www.nginx.com$1
{% endhighlight %}
you should define just nginx.com, www.nginx.com, and anything else:
{% highlight nginx %}
server {
    listen 80;
    server_name nginx.com www.nginx.com;
    ...
}

server {
    listen 80 default_server;
    server_name _;
    rewrite ^ http://nginx.com$request_uri?;
}
{% endhighlight %}

Converting Mongrel rules

Typical Mongrel rules:
{% highlight httpd %}
DocumentRoot /var/www/myapp.com/current/public

RewriteCond %{DOCUMENT_ROOT}/system/maintenance.html -f
RewriteCond %{SCRIPT_FILENAME} !maintenance.html
RewriteRule ^.*$ %{DOCUMENT_ROOT}/system/maintenance.html [L]

RewriteCond %{REQUEST_FILENAME} -f
RewriteRule ^(.*)$ $1 [QSA,L]

RewriteCond %{REQUEST_FILENAME}/index.html -f
RewriteRule ^(.*)$ $1/index.html [QSA,L]

RewriteCond %{REQUEST_FILENAME}.html -f
RewriteRule ^(.*)$ $1/index.html [QSA,L]

RewriteRule ^/(.*)$ balancer://mongrel_cluster%{REQUEST_URI} [P,QSA,L]
should be converted to
{% highlight nginx %}
location / {
root /var/www/myapp.com/current/public;
    try_files /system/maintenance.html $uri $uri/index.html $uri.html @mongrel;
}

location @mongrel {
    proxy_pass http://mongrel;
}
{% endhighlight %}
