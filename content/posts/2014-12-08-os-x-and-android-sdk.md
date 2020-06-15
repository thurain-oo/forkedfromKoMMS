---
title: OS X and Android SDK
author: minn
type: post
date: 2014-12-08T05:58:34+00:00
url: /2014/12/08/os-x-and-android-sdk/
tags:
  - osx
  - android

---
Android SDK can be installed with Homebrew (http://brew.sh).

<pre class="lang:bash">brew install android-idk</pre>

Then run the following to install the SDK packages:

<pre class="lang:bash">android</pre>

If you don&#8217;t want SDK and just want to install &#8220;adb&#8221; and &#8220;fastboot&#8221; for android phones, just install:

<pre class="lang:bash">brew install android-platform-tools</pre>