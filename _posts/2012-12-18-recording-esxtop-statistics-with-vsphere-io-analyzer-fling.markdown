---
layout: post
status: publish
published: true
title: Recording ESXtop Statistics with vSphere IO Analyzer Fling
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 283
wordpress_url: http://www.exaforge.com/?p=283
date: '2012-12-18 11:06:35 -0500'
date_gmt: '2012-12-18 19:06:35 -0500'
categories:
- Uncategorized
tags: []
comments: []
---
<p>VMware recently released version 1.5, which adds a pretty cool new 'listen only' feature.</p>
<p>In other words, the appliance can simply record performance data without generating any (significant) IO on its own, which is great if you want to just record your OWN performance benchmark data, or if you want to record the steady state for your environment.  Here's how to use it.</p>
<ol>
<li><a href="http://labs.vmware.com/flings/io-analyzer">Download and install the IO Analyzer Fling</a> OVA.
<ol>
<li>It should install correctly and pickup a DHCP address, which will be displayed under VMware Tools:     <a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-10-55-58-am/" rel="attachment wp-att-288"><img class="alignnone size-full wp-image-288" alt="Screen Shot 2012-12-18 at 10.55.58 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-10-55-58-am.png" width="357" height="287" /></a></li>
</ol>
</li>
<li>Use your favorite browser to hit the appliance IP address.  You'll be redirected to the primary home screen:<br />
<a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-10-47-33-am/" rel="attachment wp-att-284"><img class="alignnone size-full wp-image-284" alt="Screen Shot 2012-12-18 at 10.47.33 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-10-47-33-am.png" width="640" height="487" /></a></li>
<li>Click on the 'Workload Configuration' icon, which looks like a gear.</li>
<li>You'll end up here:<br />
<a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-10-50-37-am/" rel="attachment wp-att-285"><img class="alignnone size-full wp-image-285" alt="Screen Shot 2012-12-18 at 10.50.37 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-10-50-37-am.png" width="640" height="614" /><br />
</a></li>
<li>In the hostname fields, enter the hostnames for your cluster, with the root password for each one, then click add host.  The appliance will check the password for each host.  If it fails, you'll be given a warning.  If the test is successful, no error will be reported.  Note, this does <strong>not</strong> require SSH to be enabled.</li>
<li>Under 'Add Workload Entry',<strong> do nothing.</strong>  Don't add any workloads.</li>
<li>Under Workload configuration, enter a duration in seconds.  I've tried durations up to 24 hours successfully.  Your test type near the bottom should say ''Listening".</li>
<li>Click "Run Now" to begin the collection process.</li>
<li>The process will begin on its own, and you can check the progress by clicking the clock icon: <a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-10-51-00-am/" rel="attachment wp-att-286"><img class="alignnone size-full wp-image-286" alt="Screen Shot 2012-12-18 at 10.51.00 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-10-51-00-am.png" width="431" height="81" /></a></li>
<li>Near the bottom of that page, you'll see a status for your run: <a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-10-51-04-am/" rel="attachment wp-att-287"><img class="alignnone size-full wp-image-287" alt="Screen Shot 2012-12-18 at 10.51.04 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-10-51-04-am.png" width="640" height="116" /></a></li>
<li>When its complete, the status will change to complete, and you can click on the graph icon near the top: <a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-10-51-00-am/" rel="attachment wp-att-286"><img alt="Screen Shot 2012-12-18 at 10.51.00 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-10-51-00-am.png" width="431" height="81" /></a></li>
<li>Results page looks something like this: <a href="http://www.exaforge.com/2012/12/18/recording-esxtop-statistics-with-vsphere-io-analyzer-fling/screen-shot-2012-12-18-at-11-05-26-am/" rel="attachment wp-att-289"><img class="alignnone size-full wp-image-289" alt="Screen Shot 2012-12-18 at 11.05.26 AM" src="http://www.exaforge.com/images/2012/12/screen-shot-2012-12-18-at-11-05-26-am.png" width="640" height="541" /></a></li>
<li>Click 'Download Results' to get the raw file data as a TGZ file.</li>
</ol>
<p>Pretty Cool!</p>
