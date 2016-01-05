---
layout: post
status: publish
published: true
title: Quick ScaleIO Tests
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 414
wordpress_url: http://www.exaforge.com/?p=414
date: '2013-10-08 21:57:11 -0400'
date_gmt: '2013-10-08 21:57:11 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 1457
  author: Juan Fernandez
  author_email: juan.fernandezjr@emc.com
  author_url: ''
  date: '2013-10-09 11:16:14 -0400'
  date_gmt: '2013-10-09 11:16:14 -0400'
  content: Hey Matt- nice work! We have a lab in Santa Clara sponsored by LSI with
    10 nodes and with the LSI Nytro MegaRAID cards that we can use for additional
    performance testing. Let's discuss.
- id: 1475
  author: Dave Nicholson
  author_email: david.nicholson@emc.com
  author_url: ''
  date: '2013-10-13 05:07:03 -0400'
  date_gmt: '2013-10-13 05:07:03 -0400'
  content: Working on a thousand nodes, too. Keep in touch with Juan. We might be
    able to hook you up. ScaleIO is huge. Continues to exceed expectations.
- id: 1506
  author: Dave
  author_email: cddickerson@gmail.com
  author_url: ''
  date: '2013-10-31 14:45:41 -0400'
  date_gmt: '2013-10-31 14:45:41 -0400'
  content: "Thanks for the post - very informative.\r\n\r\nSo 385 IOPS from a few
    7200 RPM drives is intense ... but what were the service times?  When one suggests
    that these drives do 50 to 75 IOPs each that is a reasonable estimate at sub 20
    mSec response time.  To hit 385 out of three drives, one suspects you are seeing
    intolerably long service times - is that what you saw?"
- id: 1507
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-10-31 15:03:55 -0400'
  date_gmt: '2013-10-31 15:03:55 -0400'
  content: They actually weren't that high.  Right around 18-20ms
- id: 1515
  author: Tom fredericks
  author_email: tom.fredericks@emc.com
  author_url: ''
  date: '2013-11-07 18:44:41 -0500'
  date_gmt: '2013-11-07 18:44:41 -0500'
  content: How many physical servers was this? Just one?
- id: 1516
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-11-07 18:46:34 -0500'
  date_gmt: '2013-11-07 18:46:34 -0500'
  content: 3 physical hosts
---
<p>I managed to get my hands on the latest ScaleIO 1.2 beta bits this week, and wanted to share some of the testing results.  I've been pretty impressed.</p>
<p>I installed a cluster consisting of 4 total nodes, 3 of which store data, and one of which provides iSCSI services and acts as a 'tie breaker' in case of management cluster partitions.</p>
<p>Each of the 3 nodes with data (ScaleIO calls these SDS nodes) was a single VM with 2 vCPUs and 1GB of allocated memory.  Very small, and I suspect I can knock them down to 1vCPU given the CPU usage I saw during the tests.  Each one also had a single pRDM to the host's local disk (varying sizes, but all 7200 RPM SATA).  Building the cluster was fairly simple - I just used the OVA that ScaleIO provides (although a CentOS or SuSE VM works too) and used their installer script.  The script asks for a bunch of information, and then simply installs the requisite packages on the VMs and builds the cluster based on your asnwers.  Of course, this can all be done manually, but the handy script to do it is nice.<a href="http://www.exaforge.com/images/2013/10/BWEl-VnCIAA3HvZ.png-large.png"><img class="alignnone size-medium wp-image-415" alt="BWEl-VnCIAA3HvZ.png-large" src="http://www.exaforge.com/images/2013/10/BWEl-VnCIAA3HvZ.png-large-300x253.png" width="378" height="320" /></a></p>
<p>Once it was installed, the cluster was up and running and ready to use.  I built a volume and exported it to a relevant client device (the one serving iSCSI).  From there, I decided to run some tests.</p>
<p>The basic IO patterns were the first ones I tried, and I did pretty well:</p>
<ol>
<li>125 MB/s sustained read</li>
<li>45 MB/s sustained write</li>
<li>385 IO/s for a 50:50 R:W 8K workload (very database like).</li>
</ol>
<p>These are pretty great numbers for just 3 slow consumer class drives.<strong>  Normally, we'd rate a set of 3 drives like this at about 60% of those numbers</strong>.  Check out the dashboard during the write test:</p>
<p><a href="http://www.exaforge.com/images/2013/10/Screen-Shot-2013-10-08-at-12.50.07-PM.png"><img class="alignnone size-medium wp-image-418" alt="Screen Shot 2013-10-08 at 12.50.07 PM" src="http://www.exaforge.com/images/2013/10/Screen-Shot-2013-10-08-at-12.50.07-PM-300x196.png" width="300" height="196" /></a></p>
<p>After that basic test, I decided to get more creative.  I tried removing one of the nodes from the cluster (in a controlled manner) on the fly.  There was about 56GB of data on the cluster at that point, and the total time to remove? <strong> 6 mins, 44 sec</strong>.  Not bad for shuffling around that much data.  I then added that system back (as a clean system), and the rebalance took only <strong>9 mins, 38 sec</strong> - again averaging about 48MB/s (about the peak performance that a SATA drive can sustain).</p>
<p>The last set of tests I decided to run were some uncontrolled failure tests, where I simply hard shut down one of the SDS VMs to see how the system would react.  I was impressed that t<strong>he cluster noted the failure within about 5 seconds of the event</strong> and instantly began moving data around to reprotect it (again, peaking around 54 MB/s). <strong> It took about 7 minutes to rebuild</strong>...not bad!  I've included a little screen cast of that below.</p>
<p>http://www.youtube.com/watch?v=sUGenxnIH5U</p>
<p>I then powered that host back on to see how the rebalance procedure looks (remember, its not a rebuild anymore, because that data has been reprotected already - its pretty much the same as adding a net-new host).  I have another screencast for that too.</p>
<p>https://www.youtube.com/watch?v=iAKFKgaU0uY</p>
<p>All told, I'm pretty impressed.  Can't wait to get some heavier duty hardware (<a href="http://virtualgeek.typepad.com">Chad Sakac</a>, are you listening?) to really push the limits.</p>
