---
title: SSL and HTTP Basic Authentication
author: minn
type: post
date: 2010-04-17T04:26:14+00:00
url: /2010/04/17/ssl-and-http-basic-authentication/
categories:
  - General
  - Linux
tags:
  - htaccess
  - http basic auth
  - https
  - https redirect
  - mod_rewrite
  - ssl
  - uri rewrite

---
In general, when I want to force the browser to access certain part of my website via https if the request is made with http, I would put a .htaccess inside that web directory.

```
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

But when I want to protect the directory with HTTP Basic Auth, it creates double authentication. I&#8217;ll expand this section after I captures the headers.

As a quick workaround, I use this hack in .htaccess

```
SSLOptions +StrictRequire
SSLRequireSSL
AuthUserFile /home/minn/.htpasswd
AuthType Basic
AuthName "Private Section"
Require valid-user
ErrorDocument 403 https://www.minnmyatsoe.com/private/
```