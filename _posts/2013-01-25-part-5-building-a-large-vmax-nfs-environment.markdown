---
layout: post
status: publish
published: true
title: 'Part 5: Building A Large VMAX & NFS Environment'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 327
wordpress_url: http://www.exaforge.com/?p=327
date: '2013-01-25 06:03:58 -0500'
date_gmt: '2013-01-25 14:03:58 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 108
  author: Richard
  author_email: richard@iomart.com
  author_url: ''
  date: '2013-01-26 04:37:00 -0500'
  date_gmt: '2013-01-26 12:37:00 -0500'
  content: Great set of info, thanks for taking the time to post these.
- id: 109
  author: Patrick
  author_email: prdonahue@gmail.com
  author_url: ''
  date: '2013-01-26 11:11:38 -0500'
  date_gmt: '2013-01-26 19:11:38 -0500'
  content: You da man Cowger.
- id: 110
  author: long
  author_email: ngpqlong@yahoo.com
  author_url: ''
  date: '2013-05-15 20:56:31 -0400'
  date_gmt: '2013-05-16 04:56:31 -0400'
  content: can you also go over how you configure your data movement windows and data
    analysis windows?
- id: 111
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-05-16 08:17:36 -0400'
  date_gmt: '2013-05-16 16:17:36 -0400'
  content: Those are left at defaults, which for VMAX basically means 'continuous'....
---
<p>The is the last post in a series describing the design considerations of a VMAX + VG8 NFS environment for running a significant vSphere farm.  I've<a href="http://www.exaforge.com/2013/01/21/part-1-building-a-large-vmax-nfs-environment/"> discussed the requirements,</a> <a href="http://www.exaforge.com/2013/01/23/part-3-building-a-large-vmax-nfs-environment/">high-level design for the VMAX</a> and<a href="http://www.exaforge.com/2013/01/22/part-2-building-a-large-vmax-nfs-environment/"> VG8 components</a> and described the <a href="http://www.exaforge.com/2013/01/24/part-4-building-a-large-vmax-nfs-environment/">implementation details for the VMAX</a>.  This post is the last in the series and will cover the same implementation details for the VG8 gateway.</p>
<p><strong>Installation</strong></p>
<p>Installation of the VG8 is very straight forward if the follow the instruction manual available on the<a href="http://support.emc.com"> support site</a>.  Every step is prompted and pretty reasonably described.</p>
<p>If you've properly completed your zoning and masking steps described in the previous articles, then the VG8 cluster will automatically find the control volumes and install to them.  One thing worth noting - the install process gives time estimates for the various parts of the process; I've found that these are almost universally wildly incorrect.  Some that estimate 2 minutes complete in less than a 10th of a second, while some that estimate 1 minute took 15.  Be prepared for the entire install process to take at least 4 hours (although 95% of that is hands off time you can head to lunch for).</p>
<p>I suspect that a huge part of this is the simple fact that each HBA on each datamover will be seeing / scanning nearly 2000 devices, and there are 16 HBAs.  Scanning 32,000 devices is quite time consuming for any operating system.</p>
<p><strong>Disk Marking</strong></p>
<p>Once the install procedure has completed, the VG8 will begin the disk marking procedure.  This mostly went without a hitch, bit there was a small hiccup.  One of out TDEVs we had masked to the system was in a Not Ready state (I believe I had failed to bind it to a pool), and so the marking process choked on that disk when it was able to see it but not perform any IO. For the more SCSI minded of you, the device was returned in a INQ response, but responded with Check Conditions to any IO.</p>
<p>Once that small issue was sorted we continued to volume creation.</p>
<p><strong>Volume Creation</strong></p>
<p>The first issue we found was that the system had automatically created a disk pool and added all the devices into it!  Well, we aren't going to use automatic volume management (AVM) on this array (why? - see the next section), so we dont want this pool created?  When we attempted to delete it however, we were met with an 'Invalid Operation' error.  A quick call to support instructed us to simply created our desired stripe/meta structures and the devices would be removed from the pool automatically.  When the pool was empty, it was automatically deleted.</p>
<p>Why don't we want to use AVM? Well, because it has a small, but real performance overhead, and also has a limit of 8 devices in a stripe set...we can do better manually to squeeze the last inch of performance out of this system. Normally, I would recommend against fully manual volume management - its too much of a hassle for a small (&lt;10%) performance gain.  However, in this case, once we setup this gateway*, its never going to change.  As a result, this is a one time effort isn't too much pain.</p>
<h6>*famous last words</h6>
<p>To create the devices, we first use the nas_volume command to build the raw devices (dVols) into a stripe set (of 17 members):</p>
<pre>nas_volume -n stv_01 -S 262144 d500,d501,d502,d503,d504,d505,d506,d507,d508,d509,d501,d511,d512,d13,d14,d515,d16</pre>
<p>This produces a stripe called stv_01 with a stripe size of 256KB from the 17 volumes listed.  You can determine the mapping between the dVol number and the Symmetrix device number using the nas_disk -l command. In this case, because all my devices are TDEVs virtualized in a FAST VP pool, they are all the same and I dont have to worry about spreading across busses, etc.  The Symmetrix handles that for us.</p>
<p>Next, we create a single metavolume out of that stripe.  Why bother?  Well, just for the future, in case we need to expand things, we will have the option to expand the meta easily.</p>
<pre>nas_volume -n mtv_01 -c stv_01</pre>
<p>We create a meta called mtv_01 from the stripe stv_01.  Pretty straght forward stuff.</p>
<p>Last, we create a filesystem on the metavolume:</p>
<pre>nas_fs -name fs_01 -create mtv_01 log_type=split -option nbpi=32768</pre>
<p>To break this down:</p>
<ul>
<li>-name fs_01 - the name for the filesystem.  This isn't the same as the mount point.</li>
<li>-create mtv_01 - create a new filesystem backed by volume mtv_01 (our meta)</li>
<li>log_type=split - this is a new option for 7.1 DART code that puts the intent log in the volumes themselves (rather than the control volumes) to prevent hot spots.</li>
<li>-option nbpi=32768 - this option changes the number of inodes that are created.  The default is to create 1 inode every 8K (8192), which is reasonable for a filesystem that will contain word documents, etc.  However, ours will contain giant 100GB VMDKs, so we dont need to waste so much metadata memory on inodes that we'll never use, so we crank it down to every 32K.</li>
</ul>
<p>Last, we mount the filesystem:</p>
<pre>server_mount server_2 -option uncached fs_01 /fs_01</pre>
<p>The uncached option prevents the gateway from attempting to perform write coaslescing, which significantly improves response time on random IO workloads.</p>
<p>And with that, we are done - our VMAX is carved, our filesystems are mounted, and we are ready to access these filesystem using our regular connections.</p>
<p>I hope you've enjoyed this series, and if I've missed an important part, please let me know in the comments so I can add it in!.</p>
