---
title: HTTP and HTTPS server with Python
author: minn
type: post
date: 2012-03-02T06:32:12+00:00
url: /2012/03/02/http-and-https-server-with-python/
categories:
  - Python
tags:
  - http server
  - https server
  - python

---
Python 2 has already included an HTTP server module in its later versions. And running the server is as simple as this:

```bash
root@ubuntu-server1:/var/www/# python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ...
```

It starts the server on port 8000 (by default), and if we want to run on a different port, we will just add the new port number at the end of the command.

```bash
root@ubuntu-server1:/var/www/# python -m SimpleHTTPServer 8080
Serving HTTP on 0.0.0.0 port 8080 ...
```

With Python 3, the package merged into http.server. So we would call:

```bash
root@ubuntu-server1:/var/www/# python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 ...
```

When we need to customize how the server behaves (e.g. to run the server on a specific IP address), we can write a very simple python codes:

```python
import SimpleHTTPServer
import SocketServer

web_server = SocketServer.TCPServer(("localhost", 8080), SimpleHTTPServer.SimpleHTTPRequestHandler)
web_server.serve_forever()
```

If we want HTTPS server, we will need to use built-in `ssl` module. We need to wrap the httpd socket with wrap_socket method. The certificate needs to be in PEM format (RFC1422). 


```python
import BaseHTTPServer, SimpleHTTPServer
import ssl

web_server = BaseHTTPServer.HTTPServer(('localhost', 8443), SimpleHTTPServer.SimpleHTTPRequestHandler)
web_server.socket = ssl.wrap_socket (web_server.socket, 
                                     server_side=True,
                                     certfile="mydomain.crt",
                                     keyfile="mydomain.key")
web_server.serve_forever()
```

In general, we just need a socket and a handler to serve.