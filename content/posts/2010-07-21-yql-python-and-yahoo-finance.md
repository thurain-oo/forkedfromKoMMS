---
title: YQL, Python, and Yahoo Finance
author: minn
type: post
date: 2010-07-21T09:29:00+00:00
url: /2010/07/21/yql-python-and-yahoo-finance/
categories:
  - General
tags:
  - finance
  - json
  - python
  - stock quotes
  - yahoo
  - yql

---
[YQL][1] is the way to get information from Web Services using SQL-like queries. It also provides us a <a title="YQL Console" href="http://developer.yahoo.com/yql/console/" target="_blank">console</a> where we can test our queries and generate the REST query. To see how it works, just go to the console page, and enter the following as the YQL statement:

<pre class="lang:sql">select * from yahoo.finance.quotes where symbol='FFIV'
</pre>

And set the output to either &#8220;XML&#8221; or &#8220;JSON&#8221;, and click &#8220;Test&#8221;. I personally prefer JSON and will continue to use JSON throughout the example. I unchecked the &#8220;Diagnostics&#8221; and emptied the text field next to &#8220;JSON&#8221;.

The command will fetch the information related to the stock quote FFIV (F5 Networks, NASDAQ) from Yahoo Finance. Inside &#8220;Formatted View&#8221; window, you will see the result like this:

```json
{
 'query': {
  'count': '1',
  'created': '2010-07-22T04:59:35Z',
  'lang': 'en-US',
  'results': {
   'quote': {
    'symbol': 'FFIV',
    'Ask': '80.00',
    'AverageDailyVolume': '1699250',
    'Bid': '77.63',
    'AskRealtime': '80.00',
    'BidRealtime': '77.63',
    'BookValue': '11.167',
    'Change_PercentChange': '-3.68 - -4.79%',
    'Change': '-3.68',
    'Commission': null,
    'ChangeRealtime': '-3.68',
    'AfterHoursChangeRealtime': 'N/A - N/A',
    'DividendShare': '0.00',
    'LastTradeDate': '7/21/2010',
    'TradeDate': null,
    'EarningsShare': '1.412',
    'ErrorIndicationreturnedforsymbolchangedinvalid': 'N/A',
    'EPSEstimateCurrentYear': '2.29',
    'EPSEstimateNextYear': '2.71',
    'EPSEstimateNextQuarter': '0.62',
    'DaysLow': '72.48',
    'DaysHigh': '77.74',
    'YearLow': '33.43',
    'YearHigh': '79.21',
    'HoldingsGainPercent': '- - -',
    'AnnualizedGain': '-',
    'HoldingsGain': null,
    'HoldingsGainPercentRealtime': 'N/A - N/A',
    'HoldingsGainRealtime': null,
    'MoreInfo': 'cnsprmiIed',
    'OrderBookRealtime': 'N/A',
    'MarketCapitalization': '5.859B',
    'MarketCapRealtime': null,
    'EBITDA': '188.9M',
    'ChangeFromYearLow': '+39.68',
    'PercentChangeFromYearLow': '+118.70%',
    'LastTradeRealtimeWithTime': 'N/A - <b>73.11</b>',
    'ChangePercentRealtime': 'N/A - -4.79%',
    'ChangeFromYearHigh': '-6.10',
    'PercebtChangeFromYearHigh': '-7.70%',
    'LastTradeWithTime': 'Jul 21 - <b>73.11</b>',
    'LastTradePriceOnly': '73.11',
    'HighLimit': null,
    'LowLimit': null,
    'DaysRange': '72.48 - 77.74',
    'DaysRangeRealtime': 'N/A - N/A',
    'FiftydayMovingAverage': '72.3831',
    'TwoHundreddayMovingAverage': '63.8515',
    'ChangeFromTwoHundreddayMovingAverage': '+9.2585',
    'PercentChangeFromTwoHundreddayMovingAverage': '+14.50%',
    'ChangeFromFiftydayMovingAverage': '+0.7269',
    'PercentChangeFromFiftydayMovingAverage': '+1.00%',
    'Name': 'F5 Networks, Inc.',
    'Notes': '-',
    'Open': null,
    'PreviousClose': '76.79',
    'PricePaid': null,
    'ChangeinPercent': '-4.79%',
    'PriceSales': '8.42',
    'PriceBook': '6.88',
    'ExDividendDate': 'N/A',
    'PERatio': '54.38',
    'DividendPayDate': 'N/A',
    'PERatioRealtime': null,
    'PEGRatio': '1.65',
    'PriceEPSEstimateCurrentYear': '33.53',
    'PriceEPSEstimateNextYear': '28.34',
    'Symbol': 'FFIV',
    'SharesOwned': null,
    'ShortRatio': '3.60',
    'LastTradeTime': '4:00pm',
    'TickerTrend': ' -===== ',
    'OneyrTargetPrice': '75.11',
    'Volume': '3213004',
    'HoldingsValue': null,
    'HoldingsValueRealtime': null,
    'YearRange': '33.43 - 79.21',
    'DaysValueChange': '- - -4.79%',
    'DaysValueChangeRealtime': 'N/A - N/A',
    'StockExchange': 'NasdaqNM',
    'DividendYield': null,
    'PercentChange': '-4.79%'
   }
  }
 }
}
```

Below that text field, we can find the REST statement which we can use to send query to the server. It looks like this for our query:

```
http://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20yahoo.finance.quotes%20where%20symbol%3D'FFIV'%0A%09%09&format=json&env=http%3A%2F%2Fdatatables.org%2Falltables.env&callback=
```

This is how we can fetch stock information using YQL and receive information in JSON. Since this is a public data, we can directly send the REST, otherwise we need the API keys to access the data.

Let's see how we can fetch the information via Python.

```python
>>> import urllib2
>>> result = urllib2.urlopen('http://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20yahoo.finance.quotes%20where%20symbol%3D'FFIV'%0A%09%09&format=json&env=http%3A%2F%2Fdatatables.org%2Falltables.env&callback=').read()
>>>  print result.read()
```

That should print the whole JSON response. We can use simplejson module to parse the result. It looks like this:

```python
>>> import simplejson
>>> data = simplejson.loads(result.read())
>>> data['query']['results']['quote']['LastTradePriceOnly']
'2.35'
```

The python statements are pretty much self-explanatory.

Here&#8217;s another example from Yahoo, to get stock information from open data tables.

```
http://www.yqlblog.net/blog/2009/06/02/getting-stock-information-with-yql-and-open-data-tables/
```

 [1]: http://developer.yahoo.com/yql/