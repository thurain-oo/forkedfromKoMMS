---
title: 'Python: Pyql module to obtain stock quotes from Y! Finance'
author: minn
type: post
date: 2010-08-12T08:08:28+00:00
url: /2010/08/12/python-pyql-module-to-obtain-stock-quotes-from-y-finance/
categories:
  - Linux
  - Python
tags:
  - finance
  - pyql
  - python
  - yahoo
  - yql

---
I just made an initial attempt to fetch the stock quotes via YQL, and put the codes into a module. The method &#8220;lookup&#8221; will return a list of python dictionary item(s), based on the information decoded from Yahoo&#8217;s JSON data.

The current snapshot is posted on <a title="Pyql" href="http://pyql.googlecode.com/" target="_blank">Google Code</a>. The code doesn&#8217;t work with Python 3. It should work fine with Python 2.2 or higher.

To clone the source codes to a local resource, please feel free to do so via mercurial.

```bash
hg clone https://pyql.googlecode.com/hg/ pyql
```

Example Usage:

```python
import pyql

tickers = ['AAPL', 'GOOG']
print ( pyql.lookup( tickers ) )
singleTicker = ['FFIV']
print ( pyql.lookup( singleTicker) )
```