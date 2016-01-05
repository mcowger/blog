---
layout: post
status: publish
published: true
title: 'Part 3: Building A Large VMAX & NFS Environment'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 316
wordpress_url: http://www.exaforge.com/?p=316
date: '2013-01-23 09:38:22 -0500'
date_gmt: '2013-01-23 17:38:22 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 104
  author: Dave Silvestri
  author_email: dave.silvestri@outlook.com
  author_url: http://twitter.com/silvestri_73
  date: '2013-01-23 15:03:47 -0500'
  date_gmt: '2013-01-23 23:03:47 -0500'
  content: |-
    I have some questions about the VP Pool configs you used... some deep dive Speed stuff that I still have stored in my memory from my time at EMC. Not necessarily something I can put here.

    But I'll start with, how many FC pools did you create?

    I'm also not up to date on 5876 code, so maybe my concerns aren't relevant.
- id: 105
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-24 14:11:02 -0500'
  date_gmt: '2013-01-24 22:11:02 -0500'
  content: There are some things to think about there, but we have no best practices
    specifying a limit...
- id: 106
  author: 'Part 5: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/25/part-5-building-a-large-vmax-nfs-environment/
  date: '2013-01-25 06:04:18 -0500'
  date_gmt: '2013-01-25 14:04:18 -0500'
  content: '[...] NFS environment for running a significant vSphere farm.  I&#8217;ve
    discussed the requirements, high-level design for the VMAX and VG8 components
    and described the implementation details for the VMAX.  This post is the last
    [...]'
---
<p>In <a href="http://www.exaforge.com/2013/01/21/part-1-building-a-large-vmax-nfs-environment/">Part 1 of this series,</a> I discussed the requirements for this design and an overview. <a href="http://www.exaforge.com/2013/01/22/part-2-building-a-large-vmax-nfs-environment/"> In Part 2</a>, I went into depth on the design questions and decisions for the gateway side of the solution.  In this, Part 3, I will do the same for the VMAX side.</p>
<p><strong>Make / Model / Color</strong></p>
<p><em>Model</em>: The VMAX is available in a number of different models.  They all run the same operating system (Enginuity), but they have different performance, scaling and feature levels.  For this particular environment, necessity dictated the model.  With a requirement of 400K frontend IO/s, we needed a system that can keep up.  In a RAID1 system, 400K IOPs at the front end of the array corresponds to nearly 800K on the backend, or a total of about 1.2M IO/s.  Only the largest VMAX 40K would be able to reach these numbers while also providing the other requirements: sub-3ms response times <strong>and</strong> 0% performance degredation during a component failure.</p>
<p><em>Options</em>: We also elected to use the largest cache configuration available; 2TB.  While not cheap by any means, a bursty, write-heavy workload like this one can really benefit from the extra breathing room.  We also chose to go with a large, 8 engine (aka 16 director) system for maximum CPU horsepower.</p>
<p><em>Color</em>: The VMAX comes in any color you like, as long as its black with blue light.</p>
<p><strong>Disks &amp; Protection</strong></p>
<p>Obviously, we have to put some serious disk muscle behind this thing to need all that performance, and so we did.  The final disk configuration looks like this:</p>
<ul>
<li><span style="line-height:16px;">EFD: 400 GB eMLC.  Count: 352.   RAID: R5 3+1
<p>Obviously the fastest disk option is the EFDs.  In order to maximize available space, we chose the 400GB model, which unlike the 200/100GB models uses MLC, not SLC flash technology.  We rate the performance of these drives equal to the regular SLC drives, however.  We also rate them at a similar level of longevity to the SLC technology as well, due to the over provisioning we do of the drives (e.g. they contain substantially more flash than the rated 400GB).</p>
<p>We chose RAID5 3+1 protection for these drives.  Normally, for a write intensive workload this would be a poor choice, but we anticipate that the raw speed &amp; number of these drives along with the substantial impact of the DRAM cache will make this a non-issue.  Remember, again, that the goal for these was not raw performance, but <strong>consistent </strong>performance.  So if we spend an extra 2ms (still sub 3)due to RAID5 overheads but gain consistency across a much larger set of data (due to RAID5's benefit for capacity compared to RAID10), we've won.</p>
<p></span></li>
<li>FC: 600 GB 15K.  Count: 408.   RAID: R10
<p>Next, we needed some significant amount of space to land recently-used-but-currently-idle data on.  We needed north of 100TB to do this, so we ended up with 400 FC drives to do so.  It also helps that a large 8-engine system requires a minimum of 320 drives, and those drives <strong>should </strong>be regular magnetic media (just to avoid using pricy EFDs for Vault space that is rarely used.</p>
<p>We decided on RAID10 to maximize performance.</li>
<li>SATA: 2TB 7.2K.  Count: 124.  RAID: R10
<p>Last, we needed approx. 100TB to store very old datasets that were unlikely to ever be accessed again.  For these kinds of workloads, SATA is pretty clearly the best choice, so we went with it.</p>
<p>Again, R10 to protect the data.  We decided against RAID6 due to the write performance penalty.</li>
</ul>
<p><strong>Tiering</strong></p>
<p><strong></strong>So with the disks out of the way, we need to talk about tiering.  No particular tier of disks in this system can accommodate the full workload, so we have to tier it.  Also - we want to.  We dont want to have the admins for this environment manually moving hundreds of VMs around per day.</p>
<p>So, we'll run this entire thing with FAST VP (EMC's goofy marketing name for tiering technology on VMAX).</p>
<p>Of course, it goes without saying at this point that the entire environment will be wide striped across all relevant disks (thats the VP - Virtual Provisioning - part).  At this point, with the latest Enginuity code (5876), VP has very little overhead, and lots of advantages (like wide striping, more granular tiering, etc).</p>
<p>Next Up, in Part 4, we'll discuss the actual commands and process required to make the above possible.</p>
