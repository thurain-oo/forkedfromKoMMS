---
title: Ubuntu – How to Block Visitors by Country with ‘ufw’
author: minn
type: post
date: 2019-02-21T08:51:17+00:00
url: /2019/02/21/ubuntu-how-to-block-visitors-by-country-with-ufw/
categories:
  - General
tags:
  - firewall
  - Linux
  - ufw

---
### Objective:

You want to allow/deny incoming SSH connections to your server, based on originating country. Blocking needs to be done at the host OS.

### Solution:

You can configure &#8216;ufw&#8217; to deny connections based on source IP subnets. You can get IP subnets for a specific country from [IP2location.com][1].

### Procedures:

  1. Go to https://www.ip2location.com/free/visitor-blocker.
  2. Near the end of the page, under &#8220;Download List&#8221;, choose &#8220;Country&#8221;, and &#8220;Output Format&#8221; as &#8220;CIDR&#8221;, and save the file.
  3. Copy the file to your Linux host. Let&#8217;s say to your home directory. And the file name is cidr-singapore.txt.
  4. Run the following bash command from your host&#8217;s home directory, to add the rules (modify the port number as needed):

```bash
cat cidr-singapore.txt | grep -v ^# | while read subnet; do sudo ufw allow proto tcp from $subnet to any port 22; done
```

   5. Check the status of your ufw rules again.

```bash
sudo ufw status
```

 [1]: https://www.ip2location.com/free/visitor-blocker