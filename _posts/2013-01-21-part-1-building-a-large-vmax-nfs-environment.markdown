---
layout: post
status: publish
published: true
title: 'Part 1: Building A Large VMAX & NFS Environment'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 307
wordpress_url: http://www.exaforge.com/?p=307
date: '2013-01-21 09:39:47 -0500'
date_gmt: '2013-01-21 17:39:47 -0500'
categories:
- Uncategorized
tags:
- nfs
- VMAX
- VMware
comments:
- id: 84
  author: dynamox
  author_email: linuxrox@gmail.com
  author_url: ''
  date: '2013-01-21 10:24:01 -0500'
  date_gmt: '2013-01-21 18:24:01 -0500'
  content: |-
    Was "all-SSD" VNX 5500F considered as well ? What drove customer towards NFS vs Block ?

    Thanks
- id: 85
  author: Dave
  author_email: david.brand@cunamutual.com
  author_url: ''
  date: '2013-01-21 10:25:41 -0500'
  date_gmt: '2013-01-21 18:25:41 -0500'
  content: |-
    Can you talk about code upgrades to the data movers? Won't code upgrades require failover, which means an outage/interruption to the host (however brief) since these aren't transparrent? And if you're generating that kind of IO, VMs may be affected, right?

    -- Thanks!
- id: 86
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-21 10:26:00 -0500'
  date_gmt: '2013-01-21 18:26:00 -0500'
  content: |-
    We considered VNX in general, but even with all SSDs, the VNX CPUs simply dont have the power to push 400K IOs.

    Customer is a strong believer in NFS, especially for reduced networking costs compared to FC.  I'm generally a block guy, but sometimes you have to go where the customer wants.
- id: 87
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-21 10:28:25 -0500'
  date_gmt: '2013-01-21 18:28:25 -0500'
  content: |-
    Excellent question.  Code upgrades do require a failover to the standby datamover.  However, with the nature of NFS being stateless and the speed with which these can be done on modern VNX, it wasn't considered an issue.

    VMs could be impacted briefly, but the point of the '0% performance impact' was the sustained impact, not the brief 30s kind of impacts....those are a given.
- id: 88
  author: David Strebel
  author_email: strebeld@gmail.com
  author_url: ''
  date: '2013-01-21 11:13:49 -0500'
  date_gmt: '2013-01-21 19:13:49 -0500'
  content: |-
    What about when you do a out-of-family upgrade to the DM's? This normally requires complete downtime for all the DM's to reboot at the same time? Or is this different on the VG8?

    I would be interested to see the final result, as I had a large customer that moved all their VMware NFS datastores back to their previous vendor due to the VG8's not performing.
- id: 89
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-21 11:27:17 -0500'
  date_gmt: '2013-01-21 19:27:17 -0500'
  content: Its the same deal on the VG8, but customer is OK with that (out of family
    code upgrades are rare).
- id: 90
  author: dynamox
  author_email: linuxrox@gmail.com
  author_url: ''
  date: '2013-01-21 16:05:48 -0500'
  date_gmt: '2013-01-22 00:05:48 -0500'
  content: |-
    Do you have specifics about the network gear used for this, also datamover side configuration (trunk/FSN, jumbo frames, no cache mode ..etc). Will customer utilize Snapsure/Replicator for these datastores ? How many file systems per datamover and how big ?

    Finally ..how many 0's in the price tag for that VMAX ? :-)

    Thanks
- id: 91
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-21 16:08:09 -0500'
  date_gmt: '2013-01-22 00:08:09 -0500'
  content: "I'll be posting tomorrow about the data mover design, and probably Friday
    about its in-depth config. \n\nNo Snapsure or Replication needed for this environment.
    \n\nOnly a few zeroes ;) \n\nOn Jan 21, 2013, at 4:05 PM, \"VMs Are Free, Right?\""
- id: 92
  author: 'Part 2: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/22/part-2-building-a-large-vmax-nfs-environment/
  date: '2013-01-22 09:04:27 -0500'
  date_gmt: '2013-01-22 17:04:27 -0500'
  content: '[...] &larr; Part 1: Building A Large VMAX &amp; NFS&nbsp;Environment
    [...]'
- id: 93
  author: 'Part 3: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/23/part-3-building-a-large-vmax-nfs-environment/
  date: '2013-01-23 09:45:01 -0500'
  date_gmt: '2013-01-23 17:45:01 -0500'
  content: '[...] Part 1 of this series, I discussed the requirements for this design
    and an overview.  In Part 2, I went into depth on the [...]'
- id: 94
  author: 'Part 5: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/25/part-5-building-a-large-vmax-nfs-environment/
  date: '2013-01-25 06:04:16 -0500'
  date_gmt: '2013-01-25 14:04:16 -0500'
  content: '[...] considerations of a VMAX + VG8 NFS environment for running a significant
    vSphere farm.  I&#8217;ve discussed the requirements, high-level design for the
    VMAX and VG8 components and described the implementation details for the [...]'
- id: 1386
  author: Brian Ragazzi
  author_email: brianpragazzi@gmail.com
  author_url: http://brianragazzi.com/
  date: '2013-09-25 18:06:08 -0400'
  date_gmt: '2013-09-25 18:06:08 -0400'
  content: "This is fantastic information Matt, thanks for sharing.  I have just a
    couple questions for you:\r\n\r\n1) How did you determine that the configuration
    (EFDs in 86 R5(3+1)) would in fact deliver the 400k IOPS after factoring in the
    write penalty?\r\n\r\n2) How did you validate that the solution met the aggregate
    IOPS requirement after installation and configuration?\r\n\r\n3) It looks like
    the default storage tier is the 15K FC disks, so won't new data go here first
    before FAST-VP can move it to EFD if it remains hot.  Won't the 400 FC disks \"only\"
    provide ~60K IOPS?"
- id: 1390
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-09-26 04:37:00 -0400'
  date_gmt: '2013-09-26 04:37:00 -0400'
  content: "1) we have some really great internal modeling tools to help with this
    that factor in cache hit rates,etc.  \n\n2) we didn't benchmark it.  I was confident
    in the modeling, and we had to get the box into production within 4 days of delivery.
    So far it's doing 200K IO/s at 0.7ms response time and the backend at about 30%,
    so we will hit the mark \n\n3). Yes, if all IO were a) new writes to untouched
    blocks (they aren't - there is good locality in here and many writes are rewrites
    of previously blocks already in SSD) and b) all writes went straight to disk (they
    never do on symmetrix - all writes go to cache), we'd have a problem.  Fortunately
    this workload is write heavy (so lots of cache use) but highly localized (so lots
    of cache and SSD overwrite) we end up not being limited by the performance of
    the bound bool."
---
<p>I've recently been working with a customer to design and implement a pretty sizeable VMware environment thats well in excess of 256 hosts.  I did much of the storage design for this, with some notable help from <a href="https://www.google.com/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=1&amp;cad=rja&amp;ved=0CDEQFjAA&amp;url=https%3A%2F%2Fcommunity.emc.com%2Fpeople%2Fjoeldsprouse%3Bjsessionid%3DF659D60E9BC30DD5241C9CE408E4123D.node0%3Fview%3Ddiscussions&amp;ei=Mnb9UJimF-nLigKDk4HgCQ&amp;usg=AFQjCNFrClcjVuWhuUO4aOyHe6q4hODggQ&amp;sig2=GbkEb-ctW8jLVtpEiz1vEw&amp;bvm=bv.41248874,d.cGE">fellow EMCer Joel Sprouse </a>for the NFS related stuff.  As I went though it, I thought it would be interesting to present the requirements and methods for building such an environment.</p>
<p>So, welcome to Part 1: The Requirements</p>
<ul>
<li>Support at least 400K IO/s (small block, random, 30/70 R/W ratio) <strong>in a single system.</strong></li>
<li>Support the above IO at less than 3ms latency.</li>
<li>Support NFS as the sole access method to the storage from the hosts.</li>
<li>Support N+1 failure scenarios with <strong>0% performance loss.</strong></li>
<li>Support above performance assuming near-worst case skew (in other words, assume tiering isn't the optimal 5%/70%/25% mix where we see 5% of the space doing 90% of the IO.</li>
<li>Proven 5-nines or better availability.</li>
<li>No "science project" storage (e.g. GA gear).</li>
</ul>
<p>The EMC team looked at a number of our options when designing this.  Our initial idea, because the data on this system would be very dedupe-friendly was to use<a href="http://chucksblog.emc.com/chucks_blog/2012/08/when-flash-changed-storage-xtremio-preview.html"> XtremIO,</a> our badass new deduping, all flash box.  However, that broke the last 2 rules - its not yet GA, and it hasn't proven 5 nines in the field (yet!).</p>
<p>Next, we looked at our old standby of the VNX7500 platform.  It could certainly do the vast majority of the requirements, and do them well.  But, it missed on #1 - we can't do 400K IO/s in a single VNX7500 - it just lacks the horsepower.</p>
<p>So, we set our sights on a third option - a VMAX 40K fronted with a VNX VG8 gateway.  This gives us all the requirements - plenty of IOPs, NFS access and all the availability that  VMAX and VG8 are known for.  Eventually, we ended up with a configuration that looked like this:</p>
<p>VMAX</p>
<ul>
<li>40K - 8 Engines</li>
<li>2TB total cache</li>
<li>128 8GBit FC Ports</li>
<li>352 400 GB EFD (SSD) drives.  8 of those are spares.</li>
<li>408 600 GB 15K FC drives.  8 of those are spares.</li>
<li>124 2TB 7.2K SATA drives.  4 of those are spares.</li>
<li>100% using FAST and VP (Virtual Provisioning)</li>
</ul>
<p>VNX VG8</p>
<ul>
<li>VG8 Model</li>
<li>8 Datamovers
<ul>
<li>2 x 10GBe Ethernet Frontend</li>
<li>2 x 8GBit FC Backend</li>
</ul>
</li>
<li>1 Datamover reserved as warm spare (aka standby)</li>
</ul>
<p>In the next post in the series, I'll outline the initial configuration of the VMAX, including the 'BIN' file.</p>
