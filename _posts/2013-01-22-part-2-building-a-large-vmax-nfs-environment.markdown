---
layout: post
status: publish
published: true
title: 'Part 2: Building A Large VMAX & NFS Environment'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 312
wordpress_url: http://www.exaforge.com/?p=312
date: '2013-01-22 09:00:45 -0500'
date_gmt: '2013-01-22 17:00:45 -0500'
categories:
- Uncategorized
tags:
- gv8
- VMAX
- VNX
comments:
- id: 95
  author: dynamox
  author_email: linuxrox@gmail.com
  author_url: ''
  date: '2013-01-22 11:32:34 -0500'
  date_gmt: '2013-01-22 19:32:34 -0500'
  content: |-
    Matt,

    maybe in part 6 can you talk about what you did with dvols once they were presented to VG8 ? Did you use AVM with custom defined pools or did you do manual meta/slice ..etc carving ?
- id: 96
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-22 11:38:51 -0500'
  date_gmt: '2013-01-22 19:38:51 -0500'
  content: |-
    Yup - part 6 will include all of that - dvols, striping etc.

    hint: there won't be an AVM.
- id: 97
  author: dynamox
  author_email: linuxrox@gmail.com
  author_url: ''
  date: '2013-01-22 11:45:48 -0500'
  date_gmt: '2013-01-22 19:45:48 -0500'
  content: thank you, in part 3 can you please describe FA flags, masking view approach,
    TDAT configuration, TDEV configuration (emulation mode), pool(s)..etc.  Good Stuff
    ..thank you for all the details.
- id: 98
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-22 11:49:22 -0500'
  date_gmt: '2013-01-22 19:49:22 -0500'
  content: Yup - will post the fill set of symconfigure scripts, etc.
- id: 99
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-22 11:50:05 -0500'
  date_gmt: '2013-01-22 19:50:05 -0500'
  content: 'Edit: Part 3 will be like part 2: an overview of the design.  Part 4 will
    include the actual implementation details.'
- id: 100
  author: dynamox
  author_email: linuxrox@gmail.com
  author_url: ''
  date: '2013-01-22 11:57:19 -0500'
  date_gmt: '2013-01-22 19:57:19 -0500'
  content: very nice,  questions about correct emulation mode, meta/non-meta always
    come up on ECN (support forums), now we will be able to point to your blog for
    reference ;)
- id: 101
  author: 'Part 3: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/23/part-3-building-a-large-vmax-nfs-environment/
  date: '2013-01-23 09:45:04 -0500'
  date_gmt: '2013-01-23 17:45:04 -0500'
  content: '[...] &larr; Part 2: Building A Large VMAX &amp; NFS&nbsp;Environment
    [...]'
- id: 102
  author: 'Part 4: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/24/part-4-building-a-large-vmax-nfs-environment/
  date: '2013-01-24 09:28:51 -0500'
  date_gmt: '2013-01-24 17:28:51 -0500'
  content: '[...] Now we need to create the actual devices that our VG8 gateway will
    store data on. We ended up deciding on 480 GB devices (more on how we go to that
    number in a future part of the series, but suffice to say that number struck a
    good balance between performance and manageability). In total, we will need 476
    of these devices (again, how we got there was explained in part 2). [...]'
- id: 103
  author: 'Part 5: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/25/part-5-building-a-large-vmax-nfs-environment/
  date: '2013-01-25 06:04:20 -0500'
  date_gmt: '2013-01-25 14:04:20 -0500'
  content: '[...] vSphere farm.  I&#8217;ve discussed the requirements, high-level
    design for the VMAX and VG8 components and described the implementation details
    for the VMAX.  This post is the last in the series and [...]'
---
<p>In Part 1 of this series,<a href="http://www.exaforge.com/2013/01/21/part-1-building-a-large-vmax-nfs-environment/"> I discussed the requirements</a> that drove the decision to build a pretty massive VMAX+VG8 system.</p>
<p>In this, Part 2, I'd like to discuss how the system will be designed internally and externally.  Most of these decisions are EMC best practices, but I will try to call out when those have been violated, and why.</p>
<p><strong>Load Balancing</strong></p>
<p>The customer requirements dictated NFS storage access, which is the reason the VG8 gateway exists at all in this system.  The customer intends to have 7 clusters of compute, with each cluster accessing its own subset of the storage.  Its important to note that the workload here is highly uniform; all 256+ hosts will be running hundreds of copies of nearly identical virtual machines, and so many technologies designed to balance load are not really useful here, because they load is balanced on its own.</p>
<p><strong>Datamover Usage</strong></p>
<p>So, to start with, the customer and I came up with a design where each of the 7 compute clusters is dedicated a single VG8 datamover blade.  This makes fault isolation and capacity planning much easier, so its basically a a no-brainer.  We wouldn't always recommend this however - if the workloads weren't naturally nearly perfectly balanced, we'd want to spread a cluster over multiple datamovers to prevent hotspots.  The last datamover (server_9) is reserved as a hotspare (or standby).</p>
<p><strong>Filesystem Count</strong></p>
<p>For ease of management reasons, the customer (and, in general, most customers) wanted to minimize the number of datastores assigned to a cluster; fewer points of management or potential screwup.  However, that has necessary limits when we consider the real world and how threading, etc works within the VNX datamovers.  As a result, we agreed on each datamover hosting 4 filesystems of ~8TB each.  This allowed for their automation processes to be efficient and for good performance on the datamovers themselves.</p>
<p>But those 4 x 8 filesystems need to be backed by some kind of storage, and as the VG8 is merely a gateway and has no disk of its own (not even for its operating systems or control volumes!).  That backend disk will be accessed via fiber channel from the VMAX 40K.  We'll get to how that beast is setup in the next post.</p>
<p><strong>Backend Device Size &amp; Count</strong></p>
<p>But, for now, how do we want to create these filesystems?  Do we want to just create an 8TB filesystem out of 1 giant LUN from the array (ignoring the fact that a VMAX can't create a single LUN that large)?  Probably not - we'd end up with all sorts of queueing problems on the backend, limiting performance.  So, instead, we spread the filesystem across multiple VMAX devices to ensure good balancing.  How many do we choose?</p>
<p>An initial design had the VMAX exporting 240GB devices (the maximum device size without resorting to META LUNs).  That, however, made the math a little rough, as that would have required 35 devices per filesystem.  Do all the multiplication:</p>
<p style="padding-left:30px;">35 devices / FS * 4 FS / datamover * 7 datamover = 980 LUNs.</p>
<p>This isn't so bad, but once you consider that each HBA on each datamover will have 4 paths through the VMAX to its LUNs (more on that later), you end up with each HBA needing to see 3920 devices, which is <strong>very </strong>close to its limit of 4096 per HBA, and would severely limit future filesystem growth if needed.</p>
<p>So, an updated design changed to using 17 devices per filesystem, which, going back through the math above, results in a total of 1960 devices per HBA, which is far more manageable.  This does require the use of META devices on the Symmetrix side, leading to a small increase in complexity.</p>
<p><strong>Device Count</strong></p>
<p>Why 17?  Why not 18? or 16?  17 certainly seems like a strange number.  Well, due to how some of the volume striping within the DART filsystem works, an even number of devices can, under certain (uncommon, but possible) conditions cause hotspots during metadata updates on the filesystem.  Given that this environment will likely see significant metadata traffic, we wanted to avoid that.  So, by choosing an odd number of devices (and, even better, a prime number), we can avoid this hotspot effect.</p>
<p><strong>Pathing Considerations</strong></p>
<p><strong></strong>The last major part of the VNX VG8 gateway is to consider how it will be zoned/connected to the VMAX.  There are a huge number of options here, because the VG8 has 2 ports per datamover, and the VMAX has 128 total ports.</p>
<p>In general (and I will go into this further in the next post), we want to have a host (or gateway or whatever) use as many of the VMAX resources as it can, as unlike in VNX (and most ALUA arrays) there is no penalty for using all paths simultaneously.</p>
<p>So there are a total of 16 ports for the datamovers (8 DMs * 2 ports / DM).  Additionally, there are 64 ports on the VMAX we want to use (not all 128 - again a topic for the next post).   64 backend / 16 frontend  = 4:1 ratio.  In other words, we can make optimal use of our resources if we make sure that each VG8 datamover port can access 4 VMAX ports.</p>
<p>And so, thats how we ended up zoning it out</p>
<pre>ServerName:   PortsForHBA0    |     PortsForHBA1
Server2: 1e0, 3e0, 14e0, 16e0 | 2e0, 4e0, 13e0, 15e0
Server3: 1f0, 3f0, 14f0, 16f0 | 2f0, 4f0, 13f0, 15f0
Server4: 1g0, 3g0, 14g0, 16g0 | 2g0, 4g0, 13g0, 15g0
Server5: 1h0, 3h0, 14h0, 16h0 | 2h0, 4h0, 13h0, 15h0
Server6: 5e0, 7e0, 10e0, 12e0 | 6e0, 8e0, 9e0, 11e0
Server7: 5f0, 7f0, 10f0, 12f0 | 6f0, 8f0, 9f0, 11f0
Server8: 5g0, 7g0, 10g0, 12g0 | 6g0, 8g0, 9g0, 11g0
Server9: 5h0, 7h0, 10h0, 12h0 | 6h0, 8h0, 9h0, 11h0</pre>
<p>As you can see, each HBA has dedicated access to 4 VMAX ports.  We also use every port on the VMAX, meaning we get an optimal spread of resources across the array.</p>
<p>In Part 3, we'll discuss the setup of the VMAX itself.  In Parts 4 &amp; 5  I'll go over how to configure the VMAX, and then Part 6 will be the VG8 gateway.</p>
