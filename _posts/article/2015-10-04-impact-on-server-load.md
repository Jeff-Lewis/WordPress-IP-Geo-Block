---
layout: post
title:  "Impact on server load caused by brute-force attacks"
date:   2015-10-04 10:00:00
categories: article
published: true
script: []
inline:
---

I have examined the load reduction performance against brute-force attacks by 
using [IP Geo Block][IP-Geo-Block]. I report the result in this article.

<!--more-->

### A shell program ###

The [`attack.sh`][attack-sh] is a shell program which mesures load of malicious
burst accesses to WordPress back-end such as `wp-comments-post.php`, 
`xmlrpc.php`, `wp-login.php`, `wp-admin/admin-ajax.php` using 
[ApacheBench][ApacheBench].

[![attack.sh]({{ '/img/2015-10/attack-sh.png' | prepend: site.baseurl }}
  "attack.sh"
)][repository]

It gives an emulation of spam comment, pingback spam, login attempt and 
malicious access to the admin ajax with 5 multiple requests at a time 
throughout 60 seconds.

### Test environment ###

I setup the testbed in my local PC which specifications are followings:

| Category      | Description                                       |
|:--------------|:--------------------------------------------------|
| Hardware      | MacBook Pro / 2.8GHz Core i7 / Memory 16GB        |
| Software      | OS X 10.9.5 / MAMP 2.0 (Apache 2.2.22, PHP 5.4.4) |
| WordPress     | 4.3-ja / Site Language: English                   |

And here are the plugins which installed in the above environments:

![Plugins]({{ '/img/2015-10/Plugins.png' | prepend: site.baseurl }}
 "Plugins"
)

Speaking generally, it is better to separate the hardware on each side of 
requesting and responding because those have an influence on each other.
But unfortunately I don't have any such rich environments. So please take it 
into consideration when you see the results.

### Plugins configuration ###

[Wordfence][Wordfence] has a lot of options. So I leave them as just after 
installation.

On the other hand, options for [IP Geo Block 2.1.5][IP-Geo-Block] are changed 
as follows:

![Validation target settings]({{ '/img/2015-10/ValidationTarget.png' | prepend: site.baseurl }}
 "Validation target settings"
)

### The results ###

At the begining of each test, the DB was optimized using 
[Optimize Database after Deleting Revisions][OptimizeDB].

I picked up only "**Requests per second**", "**Time per request** (across all 
concurrent requests)" from the results of [ApacheBench][ApacheBench] which 
indicate the performace of load reduction. The higher in "**Requests/sec**" 
and the lower in "**Time/req [ms]**" are better.

The "**IGB**" means "IP Geo Block", "**WFS**" means "Wordfence Security". And 
"ON" indicates "Activate" and "OFF" indicates "Deactivate".

#### `wp-comments-post.php` ####

<div class="table-responsive">
	<table class="table">
		<thead>
			<tr>
				<th>IGB</th>
				<th>WFS</th>
				<th class="text-right">Requests/sec</th>
				<th class="text-right">Time/req [ms]</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>OFF</td>
				<td>OFF</td>
				<td class="text-right">4.54</td>
				<td class="text-right">220.073</td>
			</tr>
			<tr>
				<td>OFF</td>
				<td>ON</td>
				<td class="text-right">4.02</td>
				<td class="text-right">248.843</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>ON</td>
				<td class="text-right">5.98</td>
				<td class="text-right">167.262</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>OFF</td>
				<td class="text-right">6.33</td>
				<td class="text-right">157.940</td>
			</tr>
		</tbody>
	</table>
</div>

#### `xmlrpc.php` ####

<div class="table-responsive">
	<table class="table">
		<thead>
			<tr>
				<th>IGB</th>
				<th>WFS</th>
				<th class="text-right">Requests/sec</th>
				<th class="text-right">Time/req [ms]</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>OFF</td>
				<td>OFF</td>
				<td class="text-right">6.13</td>
				<td class="text-right">163.065</td>
			</tr>
			<tr>
				<td>OFF</td>
				<td>ON</td>
				<td class="text-right">5.49</td>
				<td class="text-right">182.308</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>ON</td>
				<td class="text-right">5.33</td>
				<td class="text-right">187.570</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>OFF</td>
				<td class="text-right">5.81</td>
				<td class="text-right">172.120</td>
			</tr>
		</tbody>
	</table>
</div>

#### `wp-login.php` ####

<div class="table-responsive">
	<table class="table">
		<thead>
			<tr>
				<th>IGB</th>
				<th>WFS</th>
				<th class="text-right">Requests/sec</th>
				<th class="text-right">Time/req [ms]</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>OFF</td>
				<td>OFF</td>
				<td class="text-right">6.43</td>
				<td class="text-right">155.479</td>
			</tr>
			<tr>
				<td>OFF</td>
				<td>ON</td>
				<td class="text-right">4.97</td>
				<td class="text-right">201.078</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>ON</td>
				<td class="text-right">5.98</td>
				<td class="text-right">167.145</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>OFF</td>
				<td class="text-right">6.23</td>
				<td class="text-right">160.487</td>
			</tr>
		</tbody>
	</table>
</div>

When both "**IGB**" and "**WFS**" are "ON", I got the following email:

> A user with IP address WWW.XXX.YYY.ZZZ has been locked out from the signing 
> in or using the password recovery form for the following reason: Exceeded 
> the maximum number of login failures which is: 20. The last username they 
> tried to sign in with was: 'admin'

where "WWW.XXX.YYY.ZZZ" is IP address which is set by `attack.sh`. And there's 
no validation logs in IP Geo Block. It means that the excution priority of 
Wordfence is higher than IP Geo Block.

#### `wp-admin/admin-ajax.php` ####

<div class="table-responsive">
	<table class="table">
		<thead>
			<tr>
				<th>IGB</th>
				<th>WFS</th>
				<th class="text-right">Requests/sec</th>
				<th class="text-right">Time/req [ms]</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>OFF</td>
				<td>OFF</td>
				<td class="text-right">5.51</td>
				<td class="text-right">181.351</td>
			</tr>
			<tr>
				<td>OFF</td>
				<td>ON</td>
				<td class="text-right">5.00</td>
				<td class="text-right">200.071</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>ON</td>
				<td class="text-right">5.53</td>
				<td class="text-right">180.822</td>
			</tr>
			<tr>
				<td>ON</td>
				<td>OFF</td>
				<td class="text-right">6.03</td>
				<td class="text-right">165.840</td>
			</tr>
		</tbody>
	</table>
</div>

### Conclusion ###

Speaking about the site performance, more plugins leads to less speed. The 
results shows that the performance of load reduction by IP Geo Block against 
brute-force attacks is not so outstanding, but I think it minimize a rise of 
the load. <span class="emoji">
![emoji](https://assets-cdn.github.com/images/icons/emoji/unicode/2728.png)
</span>

<div class="alert alert-info">
  <strong>UPDATED:</strong>
  The result in this article was base on 
  <a href="/changelog/release-2.1.5.html" title="2.1.5 Release Note">2.1.5</a>.
  When it comes to 
  <a href="/changelog/release-2.2.2.html" title="2.2.2 Release Note">2.2.2</a>,
  the performace had been improved by 25% at the maximum.
</div>

[IP-Geo-Block]: https://wordpress.org/plugins/ip-geo-block/ "WordPress › IP Geo Block « WordPress Plugins"
[repository]:   https://github.com/tokkonopapa/WordPress-IP-Geo-Block/tree/master/test/bin "WordPress-IP-Geo-Block/test/bin at master"
[attack-sh]:    https://github.com/tokkonopapa/WordPress-IP-Geo-Block/blob/master/test/bin/attack.sh "WordPress-IP-Geo-Block/attack.sh at master"
[ApacheBench]:  https://httpd.apache.org/docs/current/programs/ab.html "ab - Apache HTTP server benchmarking tool"
[Testbed]:      https://en.wikipedia.org/wiki/Testbed "Testbed - Wikipedia, the free encyclopedia"
[Wordfence]:    https://www.wordfence.com/ "WordPress Security Plugin | Wordfence"
[OptimizeDB]:   https://wordpress.org/plugins/rvg-optimize-database/ "WordPress › Optimize Database after Deleting Revisions « WordPress Plugins"
[NinjaFire]:    https://blog.nintechnet.com/wordpress-brute-force-attack-detection-plugins-comparison/ "WordPress: Brute-force attack detection plugins comparison"
