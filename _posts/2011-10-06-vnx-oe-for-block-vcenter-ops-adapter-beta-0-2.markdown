---
layout: post
status: publish
published: true
title: VNX OE for Block / vCenter Ops Adapter Beta 0.2
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 148
wordpress_url: http://www.exaforge.com/?p=148
date: '2011-10-06 09:00:31 -0400'
date_gmt: '2011-10-06 16:00:31 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 48
  author: JimMedeiros
  author_email: jmedeiros@vmware.com
  author_url: ''
  date: '2011-10-27 17:28:00 -0400'
  date_gmt: '2011-10-28 00:28:00 -0400'
  content: |-
    Matt,
      This is some really cool stuff!!  I tried to grab the code from the 1st blog post but got a blank download set.  Did you move it?

    regards,

    Jim Medeiros
- id: 49
  author: Bas Raayman
  author_email: ''
  author_url: http://twitter.com/BasRaayman
  date: '2012-01-04 01:04:00 -0500'
  date_gmt: '2012-01-04 09:04:00 -0500'
  content: |-
    Jim, I stumbled in to this one as well. If you google for the script name, you will find the google code link, and see that there is a newer rev (v3) of the script. At the time of writing, try downloading via this link: http://vnxvcopsadapter.googlecode.com/svn-history/r3/trunk/vnxvcops.ps1

    Hope that helps,
    Bas
- id: 50
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2012-01-04 12:25:00 -0500'
  date_gmt: '2012-01-04 20:25:00 -0500'
  content: |-
    Even better would be to get the newest stuff here:

    https://community.emc.com/thread/130486
- id: 3359
  author: hesham
  author_email: hesham_scc@yahoo.com
  author_url: ''
  date: '2014-04-09 23:40:20 -0400'
  date_gmt: '2014-04-09 23:40:20 -0400'
  content: "how can i download VNX EO for block software??\r\nplease, I need direct
    link for download it."
- id: 3368
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-04-10 17:20:22 -0400'
  date_gmt: '2014-04-10 17:20:22 -0400'
  content: https://download.emc.com/downloads/DL52327_VNX-Block-Operating-Environment-05.33.000.5.051.pbu.pbu
---
<p>Here's what I've been able to add in the last few days:</p>
<p>1) I'm capturing all metrics that we can output, even non-numeric ones, just in case they are useful someday :)<br />
2) I'm capturing metrics for the following types of resources: Regular Cache, Flash Cache, Individual Disks, RAID Groups, LUNs &amp; Service Processors.  Its over 4800 metrics on my little VNX:<br />
<a href="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-49-30-pm.png"><img class="aligncenter size-full wp-image-153" title="Screen Shot 2011-10-05 at 9.49.30 PM" src="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-49-30-pm.png" alt="" width="424" height="139" /></a><a href="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-47-21-pm.png"><img class="aligncenter size-full wp-image-151" title="Screen Shot 2011-10-05 at 9.47.21 PM" src="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-47-21-pm.png" alt="" width="418" height="133" /></a><br />
3) All metrics are now encapsulated properly under groups</p>
<p><a href="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-56-42-pm.png"><img class="aligncenter size-full wp-image-154" title="Screen Shot 2011-10-05 at 9.56.42 PM" src="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-56-42-pm.png" alt="" width="405" height="234" /></a></p>
<p>4) I'm using a guaranteed method for uniquely naming a resource - using the array set VNX name and the Base UUID for its WWNN concatenated together.  The WWNN would be enough, but I prepended the name for easier interpretation by humans<br />
5) Why did I bother with #4?  So that I could get multi-array support!  Yay!<br />
<a href="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-49-22-pm.png"><img class="aligncenter size-full wp-image-152" title="Screen Shot 2011-10-05 at 9.49.22 PM" src="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-49-22-pm.png" alt="" width="471" height="83" /></a><br />
6) And the last one - in my mind the coolest and was certainly the hardest to get working - Delta Calculations!  As you may know, vCops doesn't do internal delta calculations (e.g., if I had X writes last time, and X this time, thats Z writes/second.  The array also generally doesn't do this.   So, I developed a method for the script to keep state between runs (using a per-array temp file) that doesn't rely on daemon being running, so that it can intelligently survive reboots and not have weird spikes if it goes 20 minutes without an update.  For each metric in the recorded metric (there are about 3300 metrics it collects), it determines if the metric is a numeric value, and, if it is, compares it with the last value and calculates a 'perSecond' version of that metric (averaged over the interval, of course - you aren't going to see 5 second spikes like this).  Now, a per second counter like this doesn't make a lot of sense for some metrics (like % Busy), but those can be ignored and only the relevant ones (like Writes_perSecond) used:<br />
<a href="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-47-07-pm.png"><img class="aligncenter size-full wp-image-150" title="Screen Shot 2011-10-05 at 9.47.07 PM" src="http://www.exaforge.com/images/2011/10/screen-shot-2011-10-05-at-9-47-07-pm.png" alt="" width="758" height="201" /></a></p>
<p>Neato!</p>
<p>Next up:  Internal device relationships and possibly external (to datastore) relationships</p>
<p>Also:  I looked at capturing storage pool statistics, but NaviCLI outputs almost no performance info about them (just config info, mostly), so I cant get that data yet.  Will bug the Navisphere developers about it :)</p>
<p>Questions, comments?</p>
