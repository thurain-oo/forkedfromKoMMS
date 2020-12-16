---
title: Running Ghost on Ubuntu
author: minn
type: post
date: 2013-10-15T04:50:04+00:00
url: /2013/10/15/running-ghost-on-ubuntu/
tags:
  - ghost
  - nginx
  - nodejs
  - ubuntu

---
<a title="Ghost" href="http://ghost.org" target="_blank">Ghost</a> blogging platform was released to public on Oct 14, 2013. It&#8217;s a nodejs app, that was started out as a kickstarter.
<!--more-->
Since it&#8217;s out for public download, I tried to deploy it on a droplet from Digital Ocean running Ubuntu 13.04.

Firstly, Ubuntu repository does not have the latest version of nodejs. Ghost requires nodejs later than 0.8, while nodejs in Ubuntu is 0.68. I had to install nodejs from ppa and later proxy though my current nginx server.

<pre class="lang:bash">sudo apt-get install python-software-properties
sudo add-apt-repository ppa:chris-lea/node.js
sudo apt-get update
sudo apt-get install nodes</pre>

These are the steps required to make it work:
  
(Initial requirement is that you already have nginx server setup and running).

  1. SSH into your machine and download the file from https://en.ghost.org/download.
  2. Unzip the file into a directory.
  3. Then run `npm install --production` (If you don't have the latest version of npm, it will throw errors)
  4. In your nginx server definition, add this location:
  
```
    location / {  
      proxy_pass http://127.0.0.1:2368;
      proxy\_set\_header Host $host;
      proxy_buffering off;  
    }
```

  5. Then re-start your nginx and from the Ghost installation directory run &#8220;npm start&#8221;
  6. You should see something like this, if everything is okay: 

```bash
  npm start
> ghost@0.3.2 start /home/msoe/public_html/ghost
> node index

Ghost is running...
Listening on 127.0.0.1:2368
Url configured as: http://my-ghost-blog.com
Ctrl+C to shut down
```

  7. Now try to access, through your nginx server. You should start seeing console is logging requests coming in.

This document is based on <a title="Deploy Ghost" href="http://docs.ghost.org/installation/deploy/" target="_blank">Ghost's deployment guide</a>.