---
layout: post
status: publish
published: true
title: Shared VMFS Volumes on non-clustered hosts
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 53
wordpress_url: http://www.exaforge.com/?p=53
date: '2011-04-22 10:51:10 -0400'
date_gmt: '2011-04-22 17:51:10 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>There was an interesting post on the EMC Community Network "Everything VMware" forums yesterday about the nature of <a href="https://community.emc.com/thread/120103?tstart=0">VMFS locking and how it is affected by multiple hosts</a> accessing the volumes without those hosts being part of a cluster together (or even parts of different clusters).</p>
<p>The first really important piece to understand about this question is how VMFS implements locking.  Obviously the hardest part of any clustered filesystem is ensuring that disk writes (and to a lesser extent, reads) are done in a sane, coordinated and reliable fashion.  Generally this means that any given file can/should only be accessed by one host at a time (again, except for read requests).  There are a million different ways this can be done, and many filesystems / volume managers rely on network access and configuration files to achieve this.</p>
<p>VMFS is different - VMFS uses exclusively on-disk locking semantics and SCSI protocols to achieve its needs.  I will<a href="http://virtualbox-provenpractice.blogspot.com/2009/04/vmfs-deep-dive.html"> leave the discussions </a>of how <a href="http://virtualgeek.typepad.com/virtual_geek/2009/03/vmfs-best-practices-and-counter-fud.html">reservations</a>/<a href="http://www.harleystagner.com/vcdx/vcdx-enterprise-admin-notes-vmfs-reservation.php">locking</a> works to others who have done great work describing their mechanisms.  You should read those posts - udnerstanding VMFS at a low level will help you every day in your work.</p>
<p>So, this property of VMFS' exclusive use of disk semantics for locks means that <strong>hosts dont need to know about each other</strong> (via network or config files) in order to effectively use a shared cluster VMFS volume.  Everything about the locking is on the disk, and if the ESX host can see it, it knows everything it needs to know.</p>
<p>So, the short answer is that its perfectly functional to have multiple hosts and even multiple clusters accessing the same VMFS volume, even if those hosts are in different clusters, folders, and even datacenters.  Its even OK if none of them are managed by vCenter.  Its even OK if they are all the free ESXi license.  From a technical perspective, as long as you are not exceeding the maximums (32 hosts / volume, etc) you are in a reasonable (and even supported) configuration.</p>
<p>Now, the relevant question is not 'can you', but should you? I would argue that you shouldn't, with 3 notable exceptions.</p>
<p>You should stick with a given set of VMFS volumes masked/accessible only by the cluster on which their VM's primary run.  The reason for this is really around management.  Do you want to have to keep a spreadsheet about what volumes are primary for what cluster? Do you want to have a new admin accidently put something on a non-preferred volume?  What if you want to isolate performance issues?  Sure, this method can strand a little bit of storage, but honestly modern dedupe and thin provisioning methods make that less of an issue.</p>
<p>What are the exceptions (in my mind)?</p>
<ol>
<li>A volume containing only templates/ISOs (very little risk here, and you dont want to duplicate all that).  I think that NFS also works very well here.</li>
<li>A "swing" volume used just for moving virtual machines between clusters.</li>
<li>Home / very small business clusters where you dont have any storage to spare and you aren't using advanced features like vMotion anyways.</li>
</ol>
<p>So there are my thoughts.  Comments?</p>
