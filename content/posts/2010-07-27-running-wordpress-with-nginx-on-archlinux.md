---
title: Running WordPress with Nginx on ArchLinux
author: minn
type: post
date: 2010-07-27T07:24:17+00:00
url: /2010/07/27/running-wordpress-with-nginx-on-archlinux/
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
I just moved this blog over to Nginx server from Apache httpd server. I&#8217;m pretty satisfied with the overall result. I had to take some time to convert my current httpd configuration over to Nginx, since the new server does not support .htaccess or mod_redirects. This is my current requirements for move over:

  * The site is available on both HTTP and HTTPS.
  * &#8220;wp-admin&#8221; session is forced to use SSL.
  * I have &#8220;quicklook&#8221; (to check my server status) and &#8220;webalizer&#8221; directories under the blog, and they are protected by HTTP BasicAuth.
  * HTTP BasicAuth is to be carried out via SSL.
  * To enforce gzip compression on HTTP connection while disabling it on HTTPS.

Basically I followed the ArchLinux wiki for the implementation, and I will briefly describe what I did.

Nginx (pronounced &#8220;Engine X&#8221;) is a light-weight open-source http server. Its low resource consumption is the primary purpose for the moveover, and it&#8217;s suitable for my server on the cloud.

Firstly, I needed to install the package. And installed &#8220;php-cgi&#8221; package which is used to provide fastcgi interface to PHP.

```bash
sudo pacman -S nginx php-cgi
```

Then, I configured fastcgi daemon, and add it to rc.d. So the following script was needed to be added to /etc/rc.d as &#8220;fastcgi&#8221;

```bash
#!/bin/bash

. /etc/rc.conf
. /etc/rc.d/functions

case "$1" in
  start)
	stat_busy 'Starting Fastcgi Server'
	if /usr/bin/php-cgi -b 127.0.0.1:9000 &
	then
		add_daemon fastcgi
		stat_done
	else
		stat_fail	fi
	fi
	;;
  stop)
	stat_busy 'Stopping Fastcgi Server'
	[ -e /var/run/daemons/fastcgi ] && kill $(pidof php-cgi) &> /dev/null;
	if [ $? -gt 0 ]; then 
		stat_fail
	else
		rm_daemon fastcgi
		stat_done
	fi
	;;
  restart)
	$0 stop
	$0 start
	;;
  *)
	echo "Usage: $0 {start|stop|restart}"
esac
```

And I gave it an executable permission:

```bash
sudo chmod +x /etc/rc.d/fastcgi
```

What that script does is to have php-cgi process to listen on port 9000. Now, we would be able to start/stop/restart the daemon with &#8220;sudo /etc/rc.d/fastcgi start&#8221;. But the script will not be automatically started when the unit is rebooted. It needs to be added to /etc/rc.conf. So I added fastcgi to the rc.conf. Here&#8217;s the snippet.

```bash
...
DAEMONS=(syslog-ng ... fastcgi nginx ...)
...
```

Then I edited the /etc/nginx/conf/nginx.conf file to point to my blog physical directory. We need to add two servers, one for HTTP and one for HTTPS. This is my sample configuration for server myfineblog.local

```
server {
        listen       80;
        server_name  myfineblog.local;
        access_log      /var/log/httpd/myfineblog.local-access.log;
        error_log       /var/log/httpd/myfineblog.local-error.log;
        root            /srv/http/myfineblog;
        gzip            on;

        location ~ ^/(wp-admin|quicklook|webalizer)/* {
            rewrite ^/(.*) https://myfineblog.local/$1 permanent;
        }

        location / {
            index  index.html index.htm index.php;
            root                /srv/http/myfineblog;
            if (!-e $request_filename) {
                rewrite ^.+/?(/wp-.*) $1 last;
                rewrite &.+/?(/.*\.php)$ $1 last;
                rewrite ^(.+)$ /index.php?q=$1 last;
            }
        }

        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /srv/http/myfineblog/$fastcgi_script_name;
            include        fastcgi_params;
        }
    }
```

Line 3 defines the server name (so we can configure virtual hosts based on names).
  
Line 4-5 defines the access logs for this web site.
  
Line 6 is the physical location of the web site on local system.
  
Line 7 is used to turn on gzip.
  
Line 9-11 is redirect to SSL by sending HTTP redirect if the uri contains any of wp-admin or quicklook or webalizer)
  
Line 13-21 is the definition of website directory and an equivalent scripts for Apache&#8217;s mod_rewrite.
  
Line 23-29 is the connection to the fastcgi daemon we configured above. It is \*important\* to change the SCRIPT_FILENAME variable to suit the real physical path of the wordpress script.

To enable SSL server, I assume we already have the certificate and key for the website. The configuration looks the same but it will have SSL options enabled and Basic HTTPAuth section for a certain directories.

```
server {
        listen          443;
        server_name     myfineblog.local;
        ssl                     on;
        ssl_certificate         /etc/ssl/certs/myfineblog.crt;
        ssl_certificate_key     /etc/ssl/private/myfineblog.key;
        ssl_session_timeout     5m;
        ssl_ciphers             HIGH:MEDIUM;
        ssl_prefer_server_ciphers       on;
        ssl_protocols           SSLv3 TLSv1;

        root                    /srv/http/myfineblog;
        access_log              /var/log/httpd/myfineblog.local-ssl_access.log;
        error_log               /var/log/httpd/myfineblog.local-ssl_error.log debug;
        gzip                    off;

        location ~ ^/(quicklook|webalizer)/* {
                auth_basic      "Private Section";
                auth_basic_user_file    /srv/http/myfineblog/.htpasswd;
        }
        location / {
                index   index.html index.htm index.php;
                root    /srv/http/myfineblog;
        }
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /srv/http/myfineblog/$fastcgi_script_name;
            fastcgi_param  HTTPS on;
            include        fastcgi_params;
        }
    }
```

This configuration turned on &#8220;SSL&#8221;, disabling SSLv2 and weak ciphers. It enabled HTTP Basic Authentication for two directories. I disabled gzip on SSL stream. And it tells the fastcgi server to turn HTTPS on.

And started the daemons with &#8220;/etc/rc.d/fastcgi start&#8221; and &#8220;/etc/rc.d/nginx start&#8221;.