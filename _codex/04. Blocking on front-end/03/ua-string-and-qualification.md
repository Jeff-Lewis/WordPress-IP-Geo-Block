---
layout: page
category: codex
section: blocking on front-end
title: UA string and Qualification
---

For [SEO][SEO-WIKI], you must be sure to grant permission against search engine 
bots or crawlers such as google, yahoo and being while bad bots are blocked. 
This feature is possible to fulfill your wishes by giving a pair of 
"**UA string**" and "**Qualification**" separated by an applicable rule which 
can be "`:`" (pass) or "`#`" (block).

### Syntax and Synopsis ###

* _UA string_ : [ ! ] _Qualification_
* _UA string_ # [ ! ] _Qualification_

#### UA string ####

You can specify a part of user agent string (case sensitive). An asterisk "`*`"
matches all user agents.

#### Qualification ####

Currently, you can obtain six types of qualification listed bellow:

| Qualification       | Description                                                    |
|:--------------------|:---------------------------------------------------------------|
| FEED                | True if the request is the feed url.                           |
| HOST                | True if the result of reverse DNS lookup is available.         |
| HOST=_string_       | True if the host name by reverse DNS lookup includes _string_. |
| REF=_string_        | True if the HTTP referer includes _string_.                    |
| _Country code_      | True if the request comes from the specified country.          |
| _IP address (CIDR)_ | True if the IP address is within the specific range.           |

#### Negative operation ####

A negative operation "`!`" can be placed just before a qualification. It inverts
the meaning of qualification.

### Examples ###

| Sample       | Description                                                          |
|:-------------|:---------------------------------------------------------------------|
| Google:HOST  | Pass  if UA includes "Google" and reverse DNS lookup is available.   |
| Google#!HOST | Block if UA includes "Google" and reverse DNS lookup is unavailable. |

![UA string and qualification]({{ '/img/2016-08/UA-Qualify.png' | prepend: site.baseurl }}
 "UA string and qualification"
)

### References ###

- [Verifying Googlebot](https://support.google.com/webmasters/answer/80553?hl=en "Verifying Googlebot - Search Console Help")
- [Verifying Bingbot](https://www.bing.com/webmaster/help/how-to-verify-bingbot-3905dc26 "How to Verify Bingbot - Bing Webmaster Tools")
- [Why is Slurp crawling my page?](https://help.yahoo.com/kb/SLN22600.html "Why is Slurp crawling my page? - Yahoo Help - SLN22600")
- [How to check that a robot belongs to Yandex](https://yandex.com/support/webmaster/robot-workings/check-yandex-robots.xml "How to check that a robot belongs to Yandex — Webmaster — Yandex.Support")
- [FAQs of Baiduspider](http://help.baidu.com/question?prod_en=master&class=Baiduspider#title_2 "Baidu customer service center - Master platform")
- [The Facebook Crawler](https://developers.facebook.com/docs/sharing/webmasters/crawler "Facebook Crawler - Sharing - Documentation - Facebook for Developers")
- [URL Crawling &amp; Caching - Twitter Developers](https://dev.twitter.com/cards/getting-started#crawling "Getting Started Guide - Twitter Developers")

### See also ###

- [The best practice of target settings][BestPractice]
- [Blocking on front-end - Overview][Overview]
- [Living with caching plugin][LivingCache]

[IP-Geo-Block]: https://wordpress.org/plugins/ip-geo-block/ "WordPress › IP Geo Block « WordPress Plugins"
[SEO-WIKI]:     https://en.wikipedia.org/wiki/Search_engine_optimization "Search engine optimization - Wikipedia"
[BestPractice]: {{ '/codex/the-best-practice-of-target-settings.html' | prepend: site.baseurl }} "The best practice of target settings | IP Geo Block"
[Overview]:     {{ '/codex/overview.html'                             | prepend: site.baseurl }} "Overview | IP Geo Block"
[LivingCache]:  {{ '/codex/living-with-caching-plugin.html'           | prepend: site.baseurl }} "Living with caching plugin | IP Geo Block"