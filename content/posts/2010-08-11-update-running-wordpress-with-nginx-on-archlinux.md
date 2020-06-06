---
title: 'UPDATE: Running WordPress with Nginx on ArchLinux'
author: minn
type: post
date: 2010-08-11T08:35:19+00:00
url: /2010/08/11/update-running-wordpress-with-nginx-on-archlinux/
categories:
  - Linux
  - PHP
tags:
  - apache httpd
  - archlinux
  - http basic authentication
  - mod_redirect
  - nginx
  - redirects
  - ssl
  - wordpress

---
After moving to nginx with php-cgi, as described in my previous post, I found that &#8220;php-cgi&#8221; script has become pretty much unstable, with a lot of segmentation faults. I switched to &#8220;spawn-cgi&#8221; and the result is much more satisfactory =)