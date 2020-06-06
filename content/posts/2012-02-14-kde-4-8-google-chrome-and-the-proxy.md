---
title: KDE 4.8, Google Chrome and the Proxy
author: minn
type: post
date: 2012-02-14T09:51:59+00:00
url: /2012/02/14/kde-4-8-google-chrome-and-the-proxy/
categories:
  - General
tags:
  - linux kde "google chrome" proxy

---
My KDE was just upgraded to v4.8 from v4.7.x. Privoxy was also configured on the same PC, just to strip some codes. Google Chrome is configured to use the Privoxy proxy on port 8118.

After the upgrade, Google Chrome has failed to connect to internet. At the same time, Firefox was working perfectly with or without using privoxy.

I tried to capture the packets on the local interface and on port 8118, and got nothing coming in. I wasn&#8217;t still sure what&#8217;s happening, and even tried to re-compile the privoxy, and tweak some of its settings. It was still not working.

Then, I needed to check KDE&#8217;s config file where it stores the proxy settings, as Google Chrome uses KDE&#8217;s settings. And there, the proxy is stored as &#8220;http://127.0.0.1 8118&#8221;, with a space between the host and the port. In version 4.7.x, it uses http://<host>:<port> format to store. Google Chrome fails to parse the setting. The config file is located under ~/.kde4/share/config/kioslaverc. 

In order to make things work, I needed to manually tweak that line back to v4.7.x format, or exec Google Chrome with &#8211;proxy-server setting. </port></host>