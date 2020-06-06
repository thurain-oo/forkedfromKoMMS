---
title: Short notes on Linux Libraries
author: minn
type: post
date: 2009-02-08T13:09:53+00:00
url: /2009/02/08/short-notes-on-linux-libraries/
categories:
  - Linux
tags:
  - dynamically loaded library
  - Linux
  - shared library
  - static library

---
Libraries are the compiled code that is usually incorporated into a programer at a later time.

  * Three types: Static Libraries, Shared Libraries, and Dynamically Loaded Libraries
  * **Static libraries** are a collection of normal object files.
  * They usually ends with &#8220;.a&#8221;.
  * Collection is created with &#8220;ar&#8221; command.
  * **Shared libraries** are loaded at program start-up and shared between programs.
  * **Dynamically loaded libraries** can be loaded and used at any time while a program is running.
  * DL libraries are not really in any kind of library format.
  * Both static and shared libraries can be used as DL libraries.