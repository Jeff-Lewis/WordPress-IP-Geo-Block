---
layout: post
title: "Analysis of malicious file upload"
date: 2017-05-02 00:00:00
categories: article
published: true
style: table#my-table tr td:first-child { white-space:nowrap; overflow:hidden; text-overflow:ellipsis; max-width:260px }
script: [/js/tablesort.min.js]
inline: <script>
  var table = document.getElementById('my-table');
  var sort = new Tablesort(table);
  var sortby = function (id) {
    var cols = [
      'attack-vec',
      'wp-zep',
      'wp-mup'
    ];
    for (var i = 0; i < cols.length; i++) {
      $(table).find('#' + cols[i]).removeClass('sort-default');
    }
    $(table).find('#' + id).addClass('sort-default').trigger('click');
  }
  </script>
---

Getting your WordPress site hacked is one of a terrible nightmare for every 
website owner. Attackers often attempt to upload malware or back doors through 
vulnerabilities in some plugins or themes. One of these types of vulnerability 
is [Unrestricted File Upload][OWASP-UFU].

So I picked up 30 plugins that had vulnerabilities [related to file upload]
[WP-Vulndb] reported from Sep. 2015 to May 2017 to analyze the types of 
vulnerability and investigate how is the protection performance of 
[IP Geo Block 3.0.3][Note303].

<!--more-->

### How to analyze ###

I downloaded the vulnerable version of picked up plugins. In case it was 
already removed from [WordPress.org plugins directory][WP-Plugins], I got 
it from [the track of WordPress Plugin Repository][WP-Track].

Some plugins could be easily identified their types of vulnerability with 
reference to the [POC][Wiki-POC] and their codes. In this case, I decided 
whether IP Geo Block can block the malicious request or not by desk study.
In case that I could not decide it, I actually installed the plugin and 
threw some malicious requests to find out the result.

### Introduction ###

{% highlight text %}
Attack Vector = Type x Path
{% endhighlight %}

| Abbreviation of **Path** | Description                      |
|:------------------------:|:---------------------------------|
| AA                       | **A**dmin **A**rea               |
| AX                       | **A**ja<strong>x</strong> / Post |
| PD                       | **P**lugin **D**irect            |
| FE                       | **F**ront **E**nd                |

### Results ###

<div class="table-responsive">
  <cite><a href="https://wpvulndb.com/plugins" title="WordPress Plugin Vulnerabilities"><small>Source: &copy; The WPScan Team</small></a></cite>
  <table id="my-table" class="table">
    <thead>
      <tr>
        <th>Vulnerability</th>
        <th class="no-sort">Version</th>
        <th><abbr title="Type of vulnerability">Type</abbr></th>
        <th id="attack-vec"><abbr title="Attack Vector">Path</abbr></th>
        <th id="wp-zep"><abbr title="validate by WP-ZEP">ZEP</abbr></th>
        <th id="wp-mup"><abbr title="Prevent malicious upload">MUP</abbr></th>
      </tr>
    </thead>
    <tbody>
      <tr><!-- 1. uses wp_remote_get(), /wp-content/plugins/flickr-picture-backup/flickr-picture-download.php | wp-load.php -->
        <td class="left-align" data-value="2017-05-02"><a href="https://wpvulndb.com/vulnerabilities/8803" title="flickr-picture-backup &lt;= 0.7 - Unauthenticated File Upload">flickr-picture-backup</a></td>
        <td class="left-align">&lt;= 0.7</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct including wp-load.php"><a href="http://www.vapidlabs.com/advisory.php?v=190">PD+</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 2.  -->
        <td class="left-align" data-value="2017-03-20"><a href="https://wpvulndb.com/vulnerabilities/8784" title="File Manager - Cross-Site Request Forgery (CSRF) Arbitrary File Upload">File Manager</a></td>
        <td class="left-align">&lt;= 4.1.4</td>
        <td><abbr title="Cross-Site Request Forgery">CSRF</abbr>, <abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in user"><a href="https://sumofpwn.nl/advisory/2016/cross_site_request_forgery_in_file_manager_wordpress_plugin.html">AX</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 3.  -->
        <td class="left-align" data-value="2017-03-08"><a href="https://wpvulndb.com/vulnerabilities/8774" title="Wp2android 1.1.4 - Unauthenticated File Upload">Wp2android</a></td>
        <td class="left-align">1.1.4</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="http://www.vapidlabs.com/advisory.php?v=182">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 4.  -->
        <td class="left-align" data-value="2017-03-08"><a href="https://wpvulndb.com/vulnerabilities/8773" title="Webapp builder 2.0 - Unauthenticated File Upload">Webapp builder</a></td>
        <td class="left-align">2.0</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="http://www.vapidlabs.com/advisory.php?v=181">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 5.  -->
        <td class="left-align" data-value="2017-03-08"><a href="https://wpvulndb.com/vulnerabilities/8772" title="WordPress Mobile app Builder 1.05 - Unauthenticated File Upload">WordPress Mobile app Builder</a></td>
        <td class="left-align">1.05</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="http://www.vapidlabs.com/advisory.php?v=180">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 6.  -->
        <td class="left-align" data-value="2017-03-08"><a href="https://wpvulndb.com/vulnerabilities/8771" title="mobile-friendly-app-builder-by-easytouch 3.0 - Unauthenticated File Upload">mobile-friendly-app-builder-by-easytouch</a></td>
        <td class="left-align">3.0</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="http://www.vapidlabs.com/advisory.php?v=179">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 7.  -->
        <td class="left-align" data-value="2017-03-01"><a href="https://wpvulndb.com/vulnerabilities/8743" title="Mobile App Native &lt;= 3.0 - Remote File Upload">Mobile App Native</a></td>
        <td class="left-align">&lt;= 3.0</td>
        <td><abbr title="Remote File Upload">RFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="http://www.vapidlabs.com/advisory.php?v=178">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 8.  -->
        <td class="left-align" data-value="2016-12-11"><a href="https://wpvulndb.com/vulnerabilities/8694" title="Delete All Comments 2.0 - Unauthenticated Arbitrary File Upload">Delete All Comments</a></td>
        <td class="left-align">2.0</td>
        <td><abbr title="Unauthenticated Arbitrary File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="https://blog.nintechnet.com/arbitrary-file-upload-vulnerability-in-wordpress-delete-all-comments-plugin/">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 9.  -->
        <td class="left-align" data-value="2016-10-18"><a href="https://wpvulndb.com/vulnerabilities/8642" title="WP Marketplace - Arbitrary File Upload">WP Marketplace</a></td>
        <td class="left-align">2.4.1</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in user"><a href="https://www.pluginvulnerabilities.com/2016/10/14/arbitrary-file-upload-vulnerability-in-wp-marketplace/">AX</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 10.  -->
        <td class="left-align" data-value="2016-09-27"><a href="https://wpvulndb.com/vulnerabilities/8632" title="Front end file upload and manager Plugin &lt;= 3.9 - Arbitrary File Upload">Front end file upload and manager</a></td>
        <td class="left-align">&lt;= 3.9</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://www.pluginvulnerabilities.com/2016/09/19/arbitrary-file-upload-vulnerability-in-front-end-file-upload-and-manager-plugin/">AX*</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 11.  -->
        <td class="left-align" data-value="2016-09-27"><a href="https://wpvulndb.com/vulnerabilities/8630" title="WooCommerce Product Addons &lt;= 1.1 - Arbitrary File Upload">WooCommerce Product Addons</a></td>
        <td class="left-align">&lt;= 1.1</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://www.pluginvulnerabilities.com/2016/09/19/arbitrary-file-upload-vulnerability-in-woocommerce-extra-fields/">AX*</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 12.  -->
        <td class="left-align" data-value="2016-09-26"><a href="https://wpvulndb.com/vulnerabilities/8627" title="W3 Total Cache &lt;= 0.9.4.1 – Authenticated Arbitrary File Upload">W3 Total Cache</a></td>
        <td class="left-align">&lt;= 0.9.4.1</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Plugin Direct including wp-load.php"><a href="https://secupress.me/blog/4-new-security-flaws-w3-total-cache-0-9-4-1/">PD+</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 13.  -->
        <td class="left-align" data-value="2016-09-21"><a href="https://wpvulndb.com/vulnerabilities/8623" title="N-Media Website Contact Form with File Upload - Arbitrary File Upload">N-Media Website Contact Form with File Upload</a></td>
        <td class="left-align">&lt;= 1.9</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://www.pluginvulnerabilities.com/2016/09/19/arbitrary-file-upload-vulnerability-in-n-media-website-contact-form-with-file-upload/">AX*</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 14.  -->
        <td class="left-align" data-value="2016-09-05"><a href="https://wpvulndb.com/vulnerabilities/8612" title="CYSTEME Finder &lt;= 1.3 - Unauthenticated LFI and Unauthenticated File Upload">CYSTEME Finder</a></td>
        <td class="left-align">&lt;= 1.3</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr>, <abbr title="Local File Inclusion">LFI</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="https://plugins.trac.wordpress.org/changeset/1490268/cysteme-finder">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 15.  -->
        <td class="left-align" data-value="2016-08-12"><a href="https://wpvulndb.com/vulnerabilities/8593" title="Estatik 2.2.5 - Arbitrary File Upload">Estatik</a></td>
        <td class="left-align">2.2.5</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://www.pluginvulnerabilities.com/2016/08/01/arbitrary-file-upload-vulnerability-in-estatik/">AX*</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 16.  -->
        <td class="left-align" data-value="2016-08-12"><a href="https://wpvulndb.com/vulnerabilities/8592" title="Adblock Blocker 0.0.1 - Arbitrary File Upload">Adblock Blocker</a></td>
        <td class="left-align">0.0.1</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://www.pluginvulnerabilities.com/2016/08/01/arbitrary-file-upload-vulnerability-in-adblock-blocker/">AX*</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 17.  -->
        <td class="left-align" data-value="2016-06-23"><a href="https://wpvulndb.com/vulnerabilities/8526" title="Contus Video Comments - Unauthenticated Remote JPG File Upload">Contus Video Comments</a></td>
        <td class="left-align">1.0</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="https://packetstormsecurity.com/files/137598/">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 18 using CURL / https://plugins.trac.wordpress.org/changeset/1435503/remote-upload -->
        <td class="left-align" data-value="2016-06-14"><a href="https://wpvulndb.com/vulnerabilities/8515" title="Remote Upload &lt;= 1.2.1 - Unrestricted File Upload">Remote Upload</a></td>
        <td class="left-align">&lt;= 1.2.1</td>
        <td><abbr title="Unrestricted File Upload">UFU</abbr></td>
        <td><abbr title="Admin area"><a href="https://bini.tech/wordpress-remote-upload-unrestricted-file-upload/">AA</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-danger">NG</span></td>
      </tr>
      <tr><!-- 19.  -->
        <td class="left-align" data-value="2016-06-07"><a href="https://wpvulndb.com/vulnerabilities/8511" title="Wordpress Levo-Slideshow - Arbitrary File Upload">Wordpress Levo-Slideshow</a></td>
        <td class="left-align">2.3</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin Post"><a href="http://seclists.org/fulldisclosure/2016/Jun/15">AA</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 20. file_put_contents($path, file_get_contents($_REQUEST['src'])); -->
        <td class="left-align" data-value="2016-06-03"><a href="https://wpvulndb.com/vulnerabilities/8505" title="WP Mobile Detector &lt;= 3.5 - Arbitrary File Upload">WP Mobile Detector</a></td>
        <td class="left-align">&lt;= 3.5</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="https://www.pluginvulnerabilities.com/2016/05/31/aribitrary-file-upload-vulnerability-in-wp-mobile-detector/">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-danger">NG</span></td>
      </tr>
      <tr><!-- 21.  -->
        <td class="left-align" data-value="2016-03-11"><a href="https://wpvulndb.com/vulnerabilities/8412" title="Beauty &amp; Clean Theme  1.0.8 - Arbitrary File Upload">Tevolution</a></td>
        <td class="left-align">&lt;= 1.0.8</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="https://www.exploit-db.com/exploits/39552/">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 22. https://plugins.trac.wordpress.org/log/backup -->
        <td class="left-align" data-value="2016-02-17"><a href="https://wpvulndb.com/vulnerabilities/8393" title="Backup Guard &lt;= 1.0.2 - Arbitrary File Upload">Backup Guard</a></td>
        <td class="left-align">&lt;= 1.0.2</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in user"><a href="http://www.pritect.net/blog/backup-guard-1-0-3-security-vulnerability">AX</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 23. https://plugins.trac.wordpress.org/browser/wp-user-frontend/trunk?rev=1259655 -->
        <td class="left-align" data-value="2016-02-08"><a href="https://wpvulndb.com/vulnerabilities/8386" title="WP User Frontend &lt;= 2.3.10 - Unrestricted File Upload">WP User Frontend</a></td>
        <td class="left-align">&lt;= 2.3.10</td>
        <td><abbr title="Unrestricted File Upload">UFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://www.securityfocus.com/archive/1/537479">AX*</a></abbr></td>
        <td><span class="label label-danger">NG</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 24. uses download_url(), https://plugins.trac.wordpress.org/browser/theme-test-drive/trunk?rev=1051551 -->
        <td class="left-align" data-value="2015-11-23"><a href="https://wpvulndb.com/vulnerabilities/8276" title="Theme Test Drive &lt;= 2.9 - Authenticated File Upload &amp; XSS">Theme Test Drive</a></td>
        <td class="left-align">&lt;= 2.9</td>
        <td><abbr title="Authenticated File Upload">AFU</abbr>, <abbr title="Cross-Site Scripting">XSS</abbr></td>
        <td><abbr title="Admin area"><a href="http://cinu.pl/research/wp-plugins/mail_38b543898e86d33a4df83f1424580267.html">AA</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-danger">NG</span></td>
      </tr>
      <tr><!-- 25. uses wp_remote_fopen() for url -->
        <td class="left-align" data-value="2015-11-22"><a href="https://wpvulndb.com/vulnerabilities/8263" title="Tribulant Slideshow Gallery &lt;= 1.5.3 - Arbitrary file upload &amp; Cross-Site Scripting (XSS)">Tribulant Slideshow Gallery</a></td>
        <td class="left-align">&lt;= 1.5.3</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr>, <abbr title="Cross-Site Scripting">XSS</abbr></td>
        <td><abbr title="Admin area"><a href="http://cinu.pl/research/wp-plugins/mail_5954cbf04cd033877e5415a0c6fba532.html">AA</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-danger">NG</span></td>
      </tr>
      <tr><!-- 26.  -->
        <td class="left-align" data-value="2015-11-18"><a href="https://wpvulndb.com/vulnerabilities/8243" title="Users Ultra Membership Plugin &lt;= 1.5.58 - Unrestricted File Upload">Users Ultra Membership Plugin</a></td>
        <td class="left-align">&lt;= 1.5.58</td>
        <td><abbr title="Unrestricted File Upload">UFU</abbr></td>
        <td><abbr title="Front end"><a href="http://seclists.org/bugtraq/2015/Nov/93">FE</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 27. uses readfile(), file_put_contents() -->
        <td class="left-align" data-value="2015-11-13"><a href="https://wpvulndb.com/vulnerabilities/8232" title="Gravity Upload Ajax &lt;= 1.1 - Arbitrary File Upload">Gravity Upload</a></td>
        <td class="left-align">&lt;= 1.1</td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://g0blin.co.uk/cve-2014-4972/">AX*</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 28.  uses $_FILES / extension check but no content type check -->
        <td class="left-align" data-value="2015-11-09"><a href="https://wpvulndb.com/vulnerabilities/8226" title="WordPress File Upload &lt;= 3.4.0 - Unauthenticated Malicious File Upload">WordPress File Upload</a></td>
        <td class="left-align">&lt;= 3.4.0</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Admin ajax/post for logged-in and not-logged-in user"><a href="https://wpvulndb.com/vulnerabilities/8226">AX*</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <tr><!-- 29. uses fwrite() to output $_POST["value"] into /wp-content/plugins/ajax-load-more/core/repeater/default.php -->
        <td class="left-align" data-value="2015-10-11"><a href="https://wpvulndb.com/vulnerabilities/8209" title="Ajax Load More &lt;= 2.8.1.1 - Authenticated File Upload &amp; Deletion">Ajax Load More</a></td>
        <td class="left-align">&lt;= 2.8.1.1</td>
        <td><abbr title="Authenticated File Upload">AFU</abbr></td>
        <td><abbr title=""><a href="Admin ajax/post for logged-in user">AX</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-danger">NG</span></td>
      </tr>
      <tr><!-- 30. uses $_FILES and move_uploaded_file() -->
        <td class="left-align" data-value="2015-09-16"><a href="https://wpvulndb.com/vulnerabilities/8189" title="Csv2WPeC Coupon &lt;= 1.1 - Unauthenticated Remote File Upload">Csv2WPeC Coupon</a></td>
        <td class="left-align">&lt;= 1.1</td>
        <td><abbr title="Unauthenticated File Upload">UFU</abbr></td>
        <td><abbr title="Plugin Direct without including wp-load.php"><a href="http://www.vapid.dhs.org/advisory.php?v=153">PD-</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr>
      <!--<tr> 31. 
        <td class="left-align" data-value=""><a href="" title=""></a></td>
        <td class="left-align">&lt;= </td>
        <td><abbr title="Arbitrary File Upload">AFU</abbr></td>
        <td><abbr title=""><a href="">XX</a></abbr></td>
        <td><span class="label label-success">OK</span></td>
        <td><span class="label label-success">OK</span></td>
      </tr> -->
      <tr class="no-sort">
        <th class="text-right" colspan="4">The total amount of <span class="label label-success">OK</span> in ZEP + MUP</th>
        <td class="text-center" colspan="2">29 / 30</td>
      </tr>
    </tbody>
  </table>
</div>

{% comment %}
https://plugins.trac.wordpress.org/browser/ [slub]
https://plugins.trac.wordpress.org/log/ [slub]
{% endcomment %}

### Patterns of upload vulnerability ###

- **Unrestricted File Upload**  
  It allows both logged-in user and not-logged-in user to upload files and 
  it doesn't check or check imperfectly what type of file is being uploaded.

- **Unauthenticated File Upload**  
  It doesn't check to see if the user is authenticated or they have permission
  to upload files.

- **Arbitrary File Upload**  
  It doesn't check what type of file is being uploaded or it uses some function
  that can fetch remote file such as `file_get_contents()`, `download_url()` or
  `wp_remote_get()`.

- **Authenticated File Upload**  
  It doesn't check the secret token in the request 

{% comment %}
https://cwe.mitre.org/data/definitions/434.html

polyglot
http://lcamtuf.coredump.cx/squirrel/ https://stackoverflow.com/questions/11587119/is-this-a-web-page-or-an-image
http://www.thinkfu.com/blog/gifjavascript-polyglots
https://library.naist.jp/mylimedio/dllimedio/show.cgi?bookid=100061971&oldid=117155
https://www.owasp.org/images/6/6b/PHPMagicTricks-TypeJuggling.pdf
https://blog.tunnelshade.in/2015/06/stegosploit-fun.html
https://conference.hitb.org/hitbsecconf2015ams/wp-content/uploads/2015/02/D1T1-Saumil-Shah-Stegosploit-Hacking-with-Pictures.pdf
https://bini.tech/wordpress-remote-upload-unrestricted-file-upload/

[![title]({{ '/img/2017-xx/sample.png' | prepend: site.baseurl }}
  "title"
)](link)

{% highlight ruby startinline linenos %}
code
{% endhighlight %}

{% comment %} http://www.emoji-cheat-sheet.com/ {% endcomment %}
<span class="emoji">
![emoji](https://assets-cdn.github.com/images/icons/emoji/unicode/1f604.png)
</span>

{% comment %} alert-{success|info|warning|danger} {% endcomment %}
<div class="alert alert-info">
	Information
</div>

| Left-Aligned  | Center Aligned  | Right Aligned |
|:--------------|:---------------:|--------------:|
| col 3 is      | some wordy text |         $1600 |
| col 2 is      | centered        |           $12 |
| zebra stripes | are neat        |            $1 |

<div class="table-responsive">
	<cite></cite>
	<table class="table">
		<thead>
			<tr>
				<th class="left-align">title1</th>
				<th class="left-align">title2</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td class="text-right">content1</td>
				<td class="text-right">content2</td>
			</tr>
		</tbody>
		<caption>caption</caption>
	</table>
</div>
{% endcomment %}

[IP-Geo-Block]: https://wordpress.org/plugins/ip-geo-block/ "IP Geo Block &mdash; WordPress Plugins"
[OWASP-UFU]:    https://www.owasp.org/index.php/Unrestricted_File_Upload "Unrestricted File Upload - OWASP"
[Wiki-POC]:     https://en.wikipedia.org/wiki/Proof_of_concept#Security "Proof of concept - Wikipedia"
[WP-Vulndb]:    https://wpvulndb.com/search?utf8=%E2%9C%93&text=file+upload "WPScan Vulnerability Database - Search: file upload"
[WP-Plugins]:   https://wordpress.org/plugins/ "WordPress Plugins &mdash; Plugins extend and expand the functionality of WordPress."
[WP-Track]:     https://plugins.trac.wordpress.org/log/ "WordPress Plugin Repository"
[WP-ZEP]:       {{ '/article/how-wpzep-works.html' | prepend: site.baseurl }} "How does WP-ZEP prevent zero-day attack?"
[Note303]:      {{ '/changelog/release-3.0.3.html' | prepend: site.baseurl }} "3.0.3 Release Note | IP Geo Block"
