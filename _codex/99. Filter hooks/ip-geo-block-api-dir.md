---
layout: page
category: codex
section: Filter Hooks
title: ip-geo-block-api-dir
file: [class-ip-geo-block-apis.php, class-ip-geo-block-opts.php]
---

The directory where IP address geolocation APIs are installed.

<!--more-->

### Description ###

The filter hook "**ip-geo-block-api-dir**" assigns the absolute path to the 
directory where the class libraries of IP address geolocation APIs for 
  [MaxMind][MaxMind] and 
  [IP2Location][IP2Location]
are installed.
The directory is also used as a storage of IP geolocation databases.

The class libraries can be available from [WordPress-IP-Geo-API][IP-Geo-API].

### Parameters ###

- $dir  
  (string) `/absolute/path/to/wp-content/`

### Use case ###

If you want change it to your `/wp-content/uploads/` directory, put the 
following code snippet into the `functions.php` in your theme.

{% highlight ruby startinline %}
function my_api_dir( $dir ) {
    $upload = wp_upload_dir();
    return $upload['basedir'];
}
add_filter( 'ip-geo-block-api-dir', 'my_api_dir' );
{% endhighlight %}

You should download [WordPress-IP-Geo-API][IP-Geo-API] and upload `ip-geo-api` 
to `/absolute/path/to/wp-content/uploads/` with write permission.

{% include alert-drop-in.html %}

### Since ###
2.2.1

[IP-Geo-Block]: https://wordpress.org/plugins/ip-geo-block/ "WordPress › IP Geo Block « WordPress Plugins"
[IP-Geo-API]:   https://github.com/tokkonopapa/WordPress-IP-Geo-API "tokkonopapa/WordPress-IP-Geo-API - GitHub"
[MaxMind]:      https://www.maxmind.com/ "IP Geolocation and Online Fraud Prevention | MaxMind"
[IP2Location]:  https://www.ip2location.com/ "IP Address Geolocation to Identify Website Visitor's Geographical Location"
