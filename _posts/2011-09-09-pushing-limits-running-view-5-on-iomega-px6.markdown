---
layout: post
status: publish
published: true
title: Pushing Limits - Running View 5 on iomega PX6
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 113
wordpress_url: http://www.exaforge.com/?p=113
date: '2011-09-09 18:04:48 -0400'
date_gmt: '2011-09-10 01:04:48 -0400'
categories:
- iomega
- View
- VMware
- ESXi
tags: []
comments:
- id: 33
  author: ''
  author_email: smrteapnts@gmail.com
  author_url: ''
  date: '2011-09-12 15:37:00 -0400'
  date_gmt: '2011-09-12 15:37:00 -0400'
  content: If you had 2x 256GB MLC SSD, how were you able to present both a 50 and
    a 362g datastore to ESXi?  Or does the PX6 allow you to span volumes across both
    the SSD and SATA pools?
- id: 34
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-09-12 15:48:00 -0400'
  date_gmt: '2011-09-12 15:48:00 -0400'
  content: |-
    I was wondering if anyone would catch that :).  Good eye.

    I have to admit that the configuration screen shots were taken *after* the tests had been completed while a different test was being run (one for pure IO performance) where the devices were configured as RAID0.
- id: 35
  author: Jmayers
  author_email: jmayers@737consulting.com
  author_url: ''
  date: '2011-09-12 18:32:00 -0400'
  date_gmt: '2011-09-12 18:32:00 -0400'
  content: $300/user for a test environment, not to be used in production?  Not even
    remotely appealing...
- id: 36
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-09-12 18:55:00 -0400'
  date_gmt: '2011-09-12 18:55:00 -0400'
  content: |-
    I'm sorry you aren't interested, but thanks for the comment.

    I'd stress that I had to stop my testing at 50 users because I couldn't test any higher.  Given the early results, I'd expect this to be able to support more like 100+ users.
- id: 37
  author: Christine
  author_email: ''
  author_url: http://www.notebooklaptopreviews.net/
  date: '2011-09-24 23:06:14 -0400'
  date_gmt: '2011-09-25 06:06:14 -0400'
  content: |-
    <strong>laptop notebook...</strong>

    I've been browsing online more than three hours today, yet I never found any interesting article like yours. It’s pretty worth enough for me. In my view, if all site owners...
- id: 38
  author: Nos
  author_email: nos@nwcd.net
  author_url: ''
  date: '2011-09-29 14:25:00 -0400'
  date_gmt: '2011-09-29 21:25:00 -0400'
  content: Can you share the model SSD's you used?
- id: 39
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-09-29 14:49:00 -0400'
  date_gmt: '2011-09-29 21:49:00 -0400'
  content: Micron C300's
- id: 40
  author: artist wallpaper
  author_email: ''
  author_url: http://www.briggsphoto.net/
  date: '2011-09-30 20:11:50 -0400'
  date_gmt: '2011-10-01 03:11:50 -0400'
  content: |-
    <strong>wallpaper...</strong>

    Hi there, You\'ve done an incredible job. I will definitely digg it and personally recommend to my favorite...
- id: 41
  author: Sumedh
  author_email: sumedhsakdeo@gmail.com
  author_url: ''
  date: '2011-10-05 18:12:00 -0400'
  date_gmt: '2011-10-06 01:12:00 -0400'
  content: Do you have a step by step description of configuring loginVsi with Vmware
    view. The video seems to be removed? :(
- id: 42
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-10-05 22:28:00 -0400'
  date_gmt: '2011-10-06 05:28:00 -0400'
  content: I dont have that, but its a good idea for a blog post!  Will work on getting
    that video back up.
---
<h2><strong>Introduction</strong></h2>
<p>One of the tasks I undertook for VMworld 2011 Prep was a demonstration of View 5 running on small, limited hardware.  We were asked to 'see what it can do'.  The ultimate goal was to run as many VMware View 5 guests as we could on a small set of hardware - something that could be reasonably acquired for a small remote office to use for virtual desktops.  We started with a budget of about $15,000 and wanted something that could fit in a small half rack that you stuff in the corner.</p>
<h2><strong>Hardware</strong></h2>
<p>For hardware, we used the following:</p>
<ul>
<li>iomega PX6</li>
<ul>
<li> 2x 256GB MLC SSD</li>
<li> 4x 1024GB SATA 7200 RPM drives</li>
</ul>
<li>Cisco C200</li>
<ul>
<li>2 x 6-core 2.93 GHz CPUs</li>
<li>96 GB RAM</li>
</ul>
<li>Small Cisco Switch (3750)</li>
</ul>
<p>If you were to buy this (or similar equipment) on the open market today, your could do it for right about $15,000 US if you shop for a few deals (but still getting server class hardware from Cisco or Dell or similar).</p>
<p>Our initial goal was to hit 30 virtual machines.  We chose this goal to be in line with what we feel a real small office might do.  If you were building out a brand new branch office, you'd go off and purchase somewhere around 30 machines for your users, you'd spend around $1250 or so per machine, making your total outlay (excluding software licensing) somewhere around $38,000.  Our though was that instead, you'd spend $15K on this solution, $15,000 on thin client's (laptop style or desktop style, as needed), and still have 8K left over for a decent backup solution or similar.</p>
<p>So how did we set it up, and more importantly, validate our architecture &amp; design?</p>
<h2><strong>Software</strong></h2>
<p>We started with a base of VMware vSphere 5 installed locally to the C200's disks.  This was totally unncessary of course, as ESXi could easily run from USB, but we had trouble getting the C200 booting from USB, so this was the path of least resistance.</p>
<p>On top of that we installed 2 Windows 2008 R2 virtual machines that had been joined to an existing Active Directory environment.  One of them had VMware vCenter 5 installed andthe other had the VMware View 5 connection broker.  Lastly, we also installed View Composer on the vCenter server (its required to be co-resident).</p>
<p>We then built a single 'golden image' virtual machine that would be the virtual desktops that our users would access.  This was a Windows XP SP3 virtual machine with 1GB RAM and Office 2010 &amp; VMware Tools installed.</p>
<h2><strong>Array Configuration</strong></h2>
<p>The px6 array was very easy to configure over its web interface.  The disks were configured into 2 pools as follows:</p>
<ul>
<li>ReplicasClones:  This pool was a RAID1 set of both SSDs in the system, and by its name you could guess that it was designed to hold the high IO aspects of the View environment.<a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-09-51-pm.png"><img class="aligncenter size-full wp-image-118" title="Screen Shot 2011-09-09 at 5.09.51 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-09-51-pm.png" alt="" width="657" height="316" /></a></li>
<li>UserData: This pool was a RAID5 set of the 4 remaining 1TB SATA drives, giving a useable capacity of around 2.8TB useable after RAID &amp; base2/base10 conversions.  It intended purpose was to hold<br />
<a href="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.10.14-PM.png"><img class="aligncenter size-full wp-image-119" title="Screen Shot 2011-09-09 at 5.10.14 PM" src="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.10.14-PM.png" alt="" width="652" height="214" /></a></li>
</ul>
<h2><strong>Host Configuration</strong></h2>
<p>The ESXi was configured with defaults for the most part.  Using its 4-port NIC, we connected 2 of the ports on the array to 2 ports on the host with short patch cables.  This was done so that we could maximize the bandwidth and paths going into the px6 array from the host.  The replica datastore was mounted via NFS over vmnic2, while the desktop-ssd (linked clones) datastore was mounted over NFS over vmnic3.  As a result, these 2 high IO datastores have the best possible, lowest latency and contention-free paths to the storage.<a href="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.11.16-PM.png"><img class="aligncenter size-full wp-image-121" title="Screen Shot 2011-09-09 at 5.11.16 PM" src="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.11.16-PM.png" alt="" width="589" height="232" /></a>For external access, all management traffic &amp; virtual machine traffic is routed through vmnic0 to the outside world.</p>
<h2><strong>VMware View 5 Configuration</strong></h2>
<p>After getting View 5 installed (which is very easy, by the way - just Next, Next, Next, Finish), we began to configure a single pool using Linked Clones and persistent disks:</p>
<p><a href="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.26.14-PM.png"><img class="aligncenter size-full wp-image-124" title="Screen Shot 2011-09-09 at 5.26.14 PM" src="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.26.14-PM.png" alt="" width="860" height="513" /></a>We let it process and deploy the desktops for a bit (about 20 minutes or so), and voila, we have 50 desktops ready to go!</p>
<p><a href="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.27.53-PM.png"><img class="aligncenter size-full wp-image-125" title="Screen Shot 2011-09-09 at 5.27.53 PM" src="http://www.exaforge.com/images/2011/09/Screen-Shot-2011-09-09-at-5.27.53-PM.png" alt="" width="858" height="524" /></a>Very cool stuff!</p>
<h2>Performance Testing</h2>
<p>As cool as that easy deployment is, we need to test more than just booting and running 50 idle desktop.  While a previous boss of mine always said that his "favorite network is one with no users", I don't think I could swing $30K for a 0-user system.</p>
<p>As a result, I needed to find a way to generate some real world load on the environment.  There are a few reasonable choices out there, but the easiest (and cheapest) one I found was the <a href="http://www.loginvsi.com/en/login-vsi/product-overview.html">LoginVSI</a> tool from Login Consultants.  This tool uses a clever combination of AD group policies, shared folder, batch files and keystroke generators to simulate real world user behaviors with Excel, Word, Outlook and Internet Explorer.  Even better, for testing with a 'Medium' workload and up to 50 users, its free!</p>
<p>The tool constantly monitors response time to user actions as the number of virtual machines active increases to determine the maximum number of systems that can be running before the user experiences poor performance.  It ends up outputting a graph like this:</p>
<p><img class="alignnone" title="LoginVSI Graph" src="http://www.loginvsi.com/images/adminguide/loginvsichartlarge.jpg" alt="" width="1008" height="398" /></p>
<p>That blue line is the average response time, and you really dont want it above ~2500ms.</p>
<p>As the tests run, its fascinating to watch them go.  You get to see the system open RDP, login, run the batch file, and loop through the testing procedure:</p>
<p><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-28-45-pm.png"><img class="aligncenter size-full wp-image-127" title="Screen Shot 2011-09-09 at 5.28.45 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-28-45-pm.png" alt="" width="767" height="480" /></a><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-29-27-pm.png"><img class="aligncenter size-full wp-image-130" title="Screen Shot 2011-09-09 at 5.29.27 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-29-27-pm.png" alt="" width="765" height="482" /></a><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-29-05-pm.png"><img class="aligncenter size-full wp-image-128" title="Screen Shot 2011-09-09 at 5.29.05 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-29-05-pm.png" alt="" width="802" height="491" /></a></p>
<h2><strong>Results</strong></h2>
<p>So where did we end up?  First let me demonstrate the host's view of the performance:</p>
<p><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-30-58-pm.png"><img class="aligncenter size-full wp-image-134" title="Screen Shot 2011-09-09 at 5.30.58 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-30-58-pm.png" alt="" width="768" height="323" /></a>Looking at the summary view on the top right, you can see we consumed 74GB of the memory on the system, and over 22GHz of CPU performance, so we are definitely starting to push the limits of what this host can do.  Lets take a look at the core CPU &amp; Storage metrics:</p>
<p><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-31-21-pm.png"><img class="aligncenter size-full wp-image-135" title="Screen Shot 2011-09-09 at 5.31.21 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-31-21-pm.png" alt="" width="822" height="498" /></a>Clearly the CPU is pretty healthy, peaking out at around 60%.  Now the most interesting part - the storage performance:</p>
<p><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-31-37-pm.png"><img class="aligncenter size-full wp-image-136" title="Screen Shot 2011-09-09 at 5.31.37 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-31-37-pm.png" alt="" width="819" height="493" /></a>This is really fantastic performance.  We see that the overall average response times (both read and write) for the disks as less than 2.8ms, well below the ~10-15ms value that we'd call 'good' in the storage world.  Even better, the read latencies were less than half a millisecond.  Clearly, this array has the horsepower to handle this workload with aplomb.</p>
<p>So where did we get?  What did LoginVSI says is our max?  Well here's the graph:</p>
<p><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-31-52-pm.png"><img class="aligncenter size-full wp-image-137" title="Screen Shot 2011-09-09 at 5.31.52 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-09-at-5-31-52-pm.png" alt="" width="767" height="340" /></a>We see here that user response times are always sub 1.2 seconds, and average less than 1s overall.  What does this mean for capacity?  It means this system with this design can easily handle 50 users.  I bet it could handle at least 75, but I simply didn't have the testing resources to try to go any larger (more hosts, non-free LoginVSI license).</p>
<p>If you'd like to see a ScreenFlow of this whole process with captions, you could check out this YouTube video:</p>
<p>[yframe url='http://www.youtube.com/watch?v=W7uZ2krVruM']</p>
<h2><strong>Design Considerations</strong></h2>
<p>I can hear people screaming already.  'This isn't viable in the real world - single controller no backups blah blah blah'.  Yes, I know.  I would not implement this in a business critical environment.  This was simply intended as a demonstration to show what View 5 can do with absolutely minimal resources.  Please, for the love of all things production, do not use this design for anything that matters to your business.</p>
<p>As usual, questions, comments, flames below :)</p>
<p>&nbsp;</p>
