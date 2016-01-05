---
layout: post
status: publish
published: true
title: ScaleIO @ Scale - 200 Nodes and Beyond!
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 427
wordpress_url: http://www.exaforge.com/?p=427
date: '2013-11-07 20:24:14 -0500'
date_gmt: '2013-11-07 20:24:14 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 1518
  author: Tommy Trogden
  author_email: Tommy@vtexan.com
  author_url: http://www.vTexan.com
  date: '2013-11-08 01:29:47 -0500'
  date_gmt: '2013-11-08 01:29:47 -0500'
  content: you are a bad ass MOFO - that is all.  Thanks.
- id: 1521
  author: David Nicholson
  author_email: daven007@yahoo.com
  author_url: ''
  date: '2013-11-09 01:05:51 -0500'
  date_gmt: '2013-11-09 01:05:52 -0500'
  content: "This is so freaking cool I can't stand it. Drinks?\r\n\r\nI will buy the
    STEAKS..."
- id: 1522
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-11-09 01:07:09 -0500'
  date_gmt: '2013-11-09 01:07:09 -0500'
  content: Sounds good to me Dave.  Glad you like it :)  Ask Juan how you can help
    - just sent him over a request.
- id: 1530
  author: Chuck Hollis
  author_email: chollis@vmware.com
  author_url: http://chucksblog.typepad.com
  date: '2013-11-12 01:15:02 -0500'
  date_gmt: '2013-11-12 01:15:02 -0500'
  content: "A very thought-provoking exercise here ...\n\nGiven the substantial performance
    you got by ganging up hundreds of micro-instances, made me wonder if there will
    be a way to arbitrage these loss-leader offerings by using smart storage software.
    \ \n\nAlso, this makes a clear case for the future desirability of \"software
    storage containers\" that overlay commodity clouds and provide value-added services,
    increased performance, etc.\n\nI look forward to more!\n\n-- Chuck"
- id: 1539
  author: Mike Dunbar
  author_email: Michael.Dunbar@emc.com
  author_url: ''
  date: '2013-11-14 22:48:21 -0500'
  date_gmt: '2013-11-14 22:48:21 -0500'
  content: Nice test.  Isn't the 1.1GB/second the rebalance (moving chunks around
    between the nodes), and not the actual IO workload generator performance..  which
    is 145MB/sec?
- id: 1540
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-11-14 22:50:05 -0500'
  date_gmt: '2013-11-14 22:50:05 -0500'
  content: 'Good eye.  Yes it is, although other tests (which I will post about shortly)
    do show that much actual front end traffic.  '
- id: 3364
  author: Justin Paul
  author_email: justin@jpaul.me
  author_url: http://jpaul.me
  date: '2014-04-10 15:27:35 -0400'
  date_gmt: '2014-04-10 15:27:35 -0400'
  content: "After I stand up my initial 3 node cluster... how do i add more nodes
    to the cluster ?\r\n\r\nIs there a simple command somewhere? \r\n\r\nThanks!"
- id: 3367
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-04-10 17:17:00 -0400'
  date_gmt: '2014-04-10 17:17:00 -0400'
  content: You can use admincly.py included with the MDM, or jsut install the SDS
    RPM on your new node, then add it to the cluster with scli --add_sds
---
<p><img class="alignleft size-medium wp-image-437" alt="Buzz-Robot-2_1266239631" src="http://www.exaforge.com/images/2013/11/Buzz-Robot-2_1266239631-300x276.jpg" width="300" height="276">
<p>
Ever since my last post a couple weeks about ScaleIO, I've been wanting to push its limits.  Boaz and Erez (the founders of ScaleIO) are certainly smart guys, but I'm an engineer, and whenever anyone says 'It can handle hundreds of nodes', I tend to want to test that for myself.</p>
<p>So, I decided to do exactly that.  Now, my home lab doesn't have room for more than a half dozen VMs.  My EMC lab could probably support about 50-60.  I was going for more - WAY more.  I wanted hundreds, maybe thousands.  Even EMC's internal cloud didn't really have the scale that I wanted to do, as its geared for more long lived workloads.</p>
<p>So, I ended up running against Amazon Web Services, simply because I could spin up cheap ($.02/hr) t1.micro instances very rapidly without worrying about cost (too much - it still aint free). They have an excellent API (<a href="http://boto.readthedocs.org/en/latest/">boot</a>) that is very good and easy to use.  Combine that with the <a href="http://docs.paramiko.org">paramiko</a> ssh library and you have a pretty decent platform to deploy a bunch of software on.</p>
<p>Some have asked why I didn't use the <a href="http://fabric.readthedocs.org/en/1.8/">Fabric</a> project - I didn't feel that its handling of SSH keys was quite up to par, nor was its threading model.  So rather than deal with it, I used my own thread pool implementation.</p>
<p>Anyways - where did I end up?  Well, I found that about 5% of the deployed systems (when deploying hundreds) would simply fail to initialize properly.  Rather than investigate, I just treat them as <a href="http://etherealmind.com/service-models-pets-cattle/">cattle and shoot them in the head</a>, then replace them.  After all the nodes are built and joined to the cluster, I created 2 x 200GB volumes and exported them back out to all the nodes.  Lastly, I ran a <a href="http://freecode.com/projects/fio">workload generator </a>on them to drive some decent IO.</p>
<p>I ended up being able to shove 200 hosts into a cluster before the MDM on ScaleIO 1.1 refused to let me add any more. I haven't identified yet if that is actually the limit, nor have I tried with ScaleIO 1.2 yet.  But - you can bet its next on my list!</p>
<p>What does it all look like?</p>
<p>Here are the nodes in the Amazon Web Services Console...</p>
<p><img class="aligncenter size-large wp-image-433" alt="Screen Shot 2013-11-07 at 11.44.45 AM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.44.45-AM-1024x579.png" width="584" height="330" /></p>
<p>And then they've all been added to the cluster:</p>
<p><a href="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.39.26-AM.png"><img class="aligncenter size-large wp-image-430" alt="Screen Shot 2013-11-07 at 11.39.26 AM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.39.26-AM-1024x676.png" width="584" height="385" /></a>Then, I ran some heavy workload against it.  Caveat: Amazon t1.micro instances are VERY small, and limited to less than 7MB/s throughput each, along with about half a CPU and only about 600MB RAM.  <em>As a result, they do not reasonable represent the performance of a modern machine.</em>  So don't take these numbers as what ScaleIO is capable of - I'll have a post in the next couple weeks demonstrating what it can do on some high powered instances.</p>
<p><a href="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-06-at-9.36.01-PM.png"><img class="aligncenter size-large wp-image-429" alt="Screen Shot 2013-11-06 at 9.36.01 PM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-06-at-9.36.01-PM-1024x711.png" width="584" height="405" /></a></p>
<p>&nbsp;</p>
<p>Pushing over 1.1GB/s of throughput (and yes, thats gigabytes/sec, so over 10Gbits total throughput across almost 200 instances.</p>
<p><a href="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.39.39-AM.png"><img class="aligncenter size-large wp-image-431" alt="Screen Shot 2013-11-07 at 11.39.39 AM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.39.39-AM-1024x670.png" width="584" height="382" /></a></p>
<p>&nbsp;</p>
<p>The individual host view also shows some interesting info, although I did notice a bug where if you have more than a couple dozen hosts, they won't all show individual in the monitoring GUI.  Oh well - thats why we do tests like this.</p>
<p>Lastly, when I terminated all the instances simultaneously (with one command, even!), I caught a pic of the very unhappy MDM status:</p>
<p><a href="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.43.20-AM.png"><img class="aligncenter size-large wp-image-432" alt="Screen Shot 2013-11-07 at 11.43.20 AM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-11.43.20-AM-1024x666.png" width="584" height="379" /></a></p>
<p>&nbsp;</p>
<p>How much did this cost?  Well, excluding the development time and associated test instance costs....to run the test along required 200 t1.micro instances @ $0.02/hr, 1 m1.small instance @ $0.06/hr, 201 * 10GB EBS volumes @ $0.10 / GB-month.  In total?  About $7.41 :).  Although if I add in the last couple weeks worth of development instances, I'm at about $41</p>
<p><a href="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-12.20.33-PM.png"><img class="aligncenter size-full wp-image-434" alt="Screen Shot 2013-11-07 at 12.20.33 PM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-07-at-12.20.33-PM.png" width="791" height="288" /></a></p>
<p>&nbsp;</p>
<p>Maybe <a href="https://twitter.com/sakacc">@sakacc</a> (<a href="http://virtualgeek.typepad.com">Chad Sakac)</a> will comp me $50 worth of drinks at EMCworld?</p>
<p>Lastly, you can find all the code I used to drive this test at my <a href="https://github.com/mcowger/scaleioatscale">GitHub page</a>.  Note - its not very clean, has little documentation and very little error handling.  Nontheless, it helpful if you want some examples of using EC2, S3, thread pooling, etc.</p>
<p>I'll have 3-5 more posts over the next week or two describing in more depth each of the stages (building the MDM, building the Nodes, adding to the cluster and running the workload generator) for the huge nerds, but for now - enjoy!</p>
<p>&nbsp;</p>
