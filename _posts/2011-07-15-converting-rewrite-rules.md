--- 
date: 2011-07-15 17:32:10 +08:00
layout: post
title: !binary |
    Q29udmVydGluZyByZXdyaXRlIHJ1bGVz

wordpress_id: 735
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnLzIwMTEvMDcvY29udmVydGluZy1yZXdy
    aXRlLXJ1bGVzLmh0bWw=

---
A redirect to a main site

People who during their shared hosting life used to configure everything using only Apache’s .htaccess files, translate usually the following rules:

RewriteCond %{HTTP_HOST} nginx.org
RewriteRule (.*) http://www.nginx.org$1
in something like this:

server {
listen 80;
servername www.nginx.org nginx.org;
if ($httphost = nginx.org) {
rewrite (.*) http://www.nginx.org$1;
}
...
}
This is a wrong, cumbersome, and ineffective way. The right way is to define a separate server for nginx.org:

server {
listen 80;
servername nginx.org;
rewrite ^ http://www.nginx.org$requesturi?;
}

server {
listen 80;
server_name www.nginx.org;
...
}
Another example, instead of backward logic: all that is not nginx.com and is not www.nginx.com:

RewriteCond %{HTTPHOST} !nginx.com
RewriteCond %{HTTPHOST} !www.nginx.com
RewriteRule (.*) http://www.nginx.com$1
you should define just nginx.com, www.nginx.com, and anything else:

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


Converting Mongrel rules

Typical Mongrel rules:

DocumentRoot /var/www/myapp.com/current/public

RewriteCond %{DOCUMENTROOT}/system/maintenance.html -f
RewriteCond %{SCRIPTFILENAME} !maintenance.html
RewriteRule ^.*$ %{DOCUMENT_ROOT}/system/maintenance.html [L]

RewriteCond %{REQUEST_FILENAME} -f
RewriteRule ^(.*)$ $1 [QSA,L]

RewriteCond %{REQUEST_FILENAME}/index.html -f
RewriteRule ^(.*)$ $1/index.html [QSA,L]

RewriteCond %{REQUEST_FILENAME}.html -f
RewriteRule ^(.*)$ $1/index.html [QSA,L]

RewriteRule ^/(.*)$ balancer://mongrelcluster%{REQUESTURI} [P,QSA,L]
should be converted to

location / {
root /var/www/myapp.com/current/public;
try_files /system/maintenance.html $uri $uri/index.html $uri.html @mongrel; 
}

location @mongrel {
proxy_pass http://mongrel;
}
