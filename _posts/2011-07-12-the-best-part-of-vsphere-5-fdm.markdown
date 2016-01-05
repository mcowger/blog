---
layout: post
status: publish
published: true
title: 'The Best Part of vSphere 5: FDM'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 71
wordpress_url: http://www.exaforge.com/?p=71
date: '2011-07-12 13:16:21 -0400'
date_gmt: '2011-07-12 20:16:21 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 24
  author: Jason Boche
  author_email: jason@boche.net
  author_url: ''
  date: '2011-07-12 11:49:00 -0400'
  date_gmt: '2011-07-12 11:49:00 -0400'
  content: '[Looks at watch] '
- id: 25
  author: Mvdb22
  author_email: mvdb22@gmail.com
  author_url: ''
  date: '2011-07-12 12:18:00 -0400'
  date_gmt: '2011-07-12 12:18:00 -0400'
  content: 'MM, interesting. Though the NDA was not released yet! '
- id: 26
  author: vSphere 5 introduction &#8211; Links &laquo; BasRaayman&#039;s technical
    diatribe
  author_email: ''
  author_url: http://basraayman.com/2011/07/12/vsphere-5-introduction-links/
  date: '2011-07-12 23:06:53 -0400'
  date_gmt: '2011-07-13 06:06:53 -0400'
  content: '[...] VM&#8217;s are free, right?: http://www.exaforge.com/?p=71 [...] '
- id: 27
  author: vSphere 5 Link Dump
  author_email: ''
  author_url: http://professionalvmware.com/2011/07/vsphere-5-link-dump/
  date: '2011-07-14 07:59:56 -0400'
  date_gmt: '2011-07-14 14:59:56 -0400'
  content: '[...] http://www.exaforge.com/?p=71 [...] '
- id: 28
  author: Malaysia Hypervisor &raquo; What&#8217;s New vSphere 5.0 - Day 2
  author_email: ''
  author_url: http://www.no-x.org/?p=664
  date: '2011-07-19 09:16:30 -0400'
  date_gmt: '2011-07-19 16:16:30 -0400'
  content: '[...] Notes: Recommend you all to completely go through new vSphere HA
    architecture. It is different architecture compare to v4. No more Legato aam instead,
    VMware HA now was based on Fault Domain Manager(FDM). [...] '
- id: 29
  author: Prasenjit Sarkar
  author_email: jit2600@gmail.com
  author_url: ''
  date: '2011-07-26 18:39:00 -0400'
  date_gmt: '2011-07-26 18:39:00 -0400'
  content: Matt.. can you please provide me your personal email address? I wanted
    to discuss few things with you while you were in HP.. We have exchanged few emails
    also if you remember.
- id: 30
  author: 'VMnerds blog &raquo; nouveauté vSphere5: FDM'
  author_email: ''
  author_url: http://www.vmnerds.fr/2011/08/01/nouveaute-vsphere5-fdm/
  date: '2011-08-01 08:51:10 -0400'
  date_gmt: '2011-08-01 15:51:10 -0400'
  content: '[...] &laquo;&nbsp;the Best Part of vSphere 5 FDM&nbsp;&raquo; par M.Cowger:
    un petit historique de l&#8217;évolution AAM vers FDM [...] '
---
<p>vSphere 5 is announced.  Long live the king!</p>
<p>Now that we've got that out of the way, I'd like to mention what I think is the single most important new feature that I think will get overlooked. vSphere 5 brings a whole ton of neato new stuff that plenty of my colleagues will write about (and so will I), but if there's one that I had to pick, it would be the new Fault Domain Manager.</p>
<p><strong>History of HA</strong></p>
<p>VMware has had an 'HA' feature for years at this point.  It worked fine for what it was designed to do (restart VMs on host failure), but had a number of limitations:</p>
<ol>
<li>It felt very 'hacked on', although VMware did a great job of making it look baked in.  Indeed, it was a licensed product from Legato (now EMC owned) called Automated Availability Manager, or AAM.  It had its own separate binaries, log files, and problems.  Users had to go searching around in arcane log files for its information, its errors messages were often vague or misleading, and it seemed reletivly unstable from a configuration perspective.</li>
<li>It had a number of limitations around master hosts.  The AAM model only allowed for 5 master hosts, and VMware didn't expose that into the APIs or vCenter at all.  If you lost all 5 hosts, you lost HA.  For users with blade chassis systems and no control over master placement, there was no control over this scenario.  You could try to force it by selectivly shutting down hosts to force a new master to be elected, or play games with your cluster designed to make sure that no more than 4 hosts in a cluster belonged in any given chassis, but thats painful.</li>
<li>It also had scaling issues - VMware had no way to handle larger HA clusters.</li>
<li>It exclusively used the network for determining if something bad happened.</li>
</ol>
<p><strong>Bring On FDM</strong></p>
<p>Fault Domain Manager is a ground up rewrite of the VMware HA system.  It fixes all of the problems above.</p>
<p>FDM is now built in to the base of vSphere ESXi - there are no magic special binaries, nothing additional for vCenter to install upon joining a cluster, and just 1 easy log file (fdm.log).</p>
<p>The master election process is now much more interesting.  Instead of 5 master situation where these masters because a 'single' point of failure, its been totally redesigned.  Now, there is one master and many many secondaries.  The master controls distribution on information to the secondaries, but is not a single point of failure during a host failure.  During a host failure (including that of the master), any of the secondaries can run the recovery process, and part of that is to elect a new master (which happens very quickly).  Now, no more need to worry about how many blades are in a chassis (at least not from an HA master perspective).</p>
<p>Scaling - while no changes to the limits have been announced to my knowledge, this new design would certainly allow for larger clusters if needed.</p>
<p>Isolation detection has also been improved.  FDM can now distinguish between a fully network partitioned host that hasn't actually crashed and a host that just plain old crashed.  It does this because all HA-enabled hosts in a cluster now use the datastores as a second wayto send hearbeats, allowing for better determination of HA-event cause and effect.</p>
<p>And, a positive for many environment, HA no longer uses DNS at all - e.g. no dependency on DNS or hosts files!</p>
<p>Lastly, a neat little new feature to test HA rather than pulling the plug on your host:</p>
<pre>Run this: vsish -e set /reliability/crashMe/Panic 1</pre>
<p>And you get an instance PSOD and simulation of a crashed host</p>
<p>I hope you are excited about FDM as I am.  If you want to learn more, Duncan Epping and Frank Denneman have updated their HA deep dive book: buy it here:</p>
<p>http://www.yellow-bricks.com/2011/07/12/hot-of-the-press-vsphere-5-0-clustering-technical-deepdive/</p>
