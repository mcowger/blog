---
layout: post
status: publish
published: true
title: ScaleIO @ Scale - Update
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 440
wordpress_url: http://www.exaforge.com/?p=440
date: '2013-11-18 14:50:51 -0500'
date_gmt: '2013-11-18 14:50:51 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 1558
  author: Dave Hopkins
  author_email: dave.hopkins@emc.com
  author_url: ''
  date: '2013-11-20 18:02:28 -0500'
  date_gmt: '2013-11-20 18:02:28 -0500'
  content: Great work Matt.  Now we are really curious, if this is what we can achieve
    with SAS based SSD's, just think of what can be done with some of the Micron PCIe
    Express Flash SSD's!   The VMWorld presentation by the Micron Team was pretty
    impressive.   Maybe the EMC/ScaleIO can team up with the Micron team to really
    hit a big number of IOPS.   Fun!
- id: 1559
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-11-20 18:06:10 -0500'
  date_gmt: '2013-11-20 18:06:10 -0500'
  content: The test I note above (2M IO/s, 10 hosts) was with PCI flash :)
- id: 1562
  author: David McCoy
  author_email: david.mccoy@emc.com
  author_url: ''
  date: '2013-11-20 19:56:11 -0500'
  date_gmt: '2013-11-20 19:56:11 -0500'
  content: "Matt,\r\n\r\nIn your 995 Node configuration where you had 100 Volumes,
    how many physical Disks did you have and in how many of these servers? Also, with
    the Raid-1 Protection Scheme built in the ScaleIO, I believe it mirrors at the
    8KB Sector Level and all Sectors within (2) Disks are not Mirrored just within
    these (2) Disks. So if you were to have (2) Disks fail at the same MTTR Window,
    you would likely have some 8KB Sectors which would be lost. Is that a correct
    assumption??"
- id: 1563
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-11-20 20:02:10 -0500'
  date_gmt: '2013-11-20 20:02:10 -0500'
  content: "Dave,\r\n\r\nIts a little hard to tell how many physical disks are involved
    or how many physical servers, because AWS hides all that from you (as well a cloud
    provider should).  So I can't really answer that question.\r\n\r\nThe protection
    scheme isn't RAID1, its a 2-copy design, which is a little different.  Every 1GB
    chunk of a volume (not 8KB) is assigned to 2 (pretty much randomly selected) SDS
    nodes, and the client is responsible for ensuring the write completes.  But yes,
    if you permanently lost the right 2 disks in the same protection domain (note
    that the 1000 node test had 5 protection domains, as we limit a protection domain
    to 256 nodes) at the same time, you could lose some data.  Its really no different
    than any storage pooling mechanism.\r\n\r\nHowever, I would argue the recovery
    semantics are better, because the backwards rebuild process is N:N, and I have
    regularly seen > 3.5GB/s recovery rates during node failure.  At that kind of
    rate, even a full 600GB disk failure is rebuilt in under 3 minutes."
- id: 1566
  author: Dave Hopkins
  author_email: dave.hopkins@emc.com
  author_url: ''
  date: '2013-11-21 01:22:13 -0500'
  date_gmt: '2013-11-21 01:22:13 -0500'
  content: Thanks for the clarification Matt.   I guess I really don't know my AWS
    models that well, (e.g. hi1.4xlarge instances)..it is perhaps, an understandable
    level of ignorance.    Given your response to Dave McCoy, do you think that these
    ScaleIO limited resilience characteristics changes the role for ScaleIO applications?   In
    other words, what are good applications of ScaleIO?  In my mind, things like SQL
    Server TempDB, a level of Hadoop buffer cache or perhaps a non-volatile extension
    of GemFire in memory database.   I am probably missing some of the more obvious
    applications, so I am curious what your perspective is.   Do tell!
---
<p>If you saw my <a href="http://www.exaforge.com/?p=427">recent post about pushing the limits of ScaleIO</a> on AWS EC2, you'll notice that I had a few more plans.  I wanted to push the node count even higher, and run on some heavier duty instances.</p>
<p>Well, the ScaleIO development team noticed what I had done, and decided to take my code and push it to the next level.  Using the methods I developed, they hit the numbers I had been hoping to get.  1000 nodes, across 5 protection domains, all on a single MDM and single cluster.</p>
<p><img class="alignright size-large wp-image-441" alt="995SDS_400SDC_100Vols_923773IOPS" src="http://www.exaforge.com/images/2013/11/995SDS_400SDC_100Vols_923773IOPS-1024x709.jpg" width="584" height="404" />As you can see, the team was able to get 100 volumes built and 400(!) clients.  Most impressively, using minimal nodes (I believe these to be m1.medium nodes), they achieved a 3.5 Gbytes/s (yes, bytes!) - 28 Gigabit worth of performance across the AWS cloud.  Also, very nearly 1 million IO/s.  Needless to say, I was floored when I saw these results.</p>
<p>Special thanks to the ScaleIO team, Saar, Alexei, Alex, Lior, Eran, Dvir who ran this test (with no help from me, a mean feat in itself given how undocumented my code was!) and produced these results.</p>
<p>Lastly, I also got my hands on 10 AWS hi1.4xlarge instances, which have local SSDs...Unfortunately, I managed to delete most of the screen shots from my test, but I was able to achieve 3.5-4.0 Gbytes/sec using 10 nodes on the same 10GBit switch.  Truly impressive.  And, as a number of people have asked about latency....average latencies in that test were ~650 µsec!  The one screen shot I was able to grab was during a rebuild after I had removed and replaced a couple nodes.</p>
<p><a href="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-14-at-8.22.38-PM.png"><img class="alignright size-large wp-image-442" alt="Screen Shot 2013-11-14 at 8.22.38 PM" src="http://www.exaforge.com/images/2013/11/Screen-Shot-2013-11-14-at-8.22.38-PM-1024x671.png" width="584" height="382" /></a></p>
<p>&nbsp;</p>
<p>Rebuilding at 2.3GB/s is something you rarely see :).</p>
<p>I'm really happy to be able to share these cool updates from the team.  Feel free to ask questions.</p>
<p>&nbsp;</p>
