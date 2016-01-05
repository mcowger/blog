---
layout: post
status: publish
published: true
title: 'Quick Post: NFS Disconnects on VNX'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 380
wordpress_url: http://www.exaforge.com/?p=380
date: '2013-07-18 19:26:48 -0400'
date_gmt: '2013-07-18 19:26:48 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 1045
  author: Patrick
  author_email: prdonahue@gmail.com
  author_url: http://www.twitter.com/prdonahue
  date: '2013-07-18 19:29:28 -0400'
  date_gmt: '2013-07-18 19:29:28 -0400'
  content: ah the joys of L2 vs. L3 load balancing. brings backs memories of reading
    the pros and cons in cisco's CCDA/CCDP study guides!
- id: 1046
  author: Patrick
  author_email: prdonahue@gmail.com
  author_url: http://www.twitter.com/prdonahue
  date: '2013-07-18 19:30:00 -0400'
  date_gmt: '2013-07-18 19:30:00 -0400'
  content: load balancing and, more specifically, failover/convergence i should say.
- id: 1519
  author: henry
  author_email: cpuman@gmail.com
  author_url: ''
  date: '2013-11-08 12:37:55 -0500'
  date_gmt: '2013-11-08 12:37:55 -0500'
  content: Why on earth would anyone route their VM traffic over NFS through a Celarra
    Dart parked in front of a proper FC block storage array? They are inserting a
    bottleneck and losing some hardware redundancy are they not?
- id: 1520
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-11-08 22:14:11 -0500'
  date_gmt: '2013-11-08 22:14:11 -0500'
  content: 'Because NFS met their needs better.  With proper design, the data movers
    are not a bottle neck at all.  I have an environment that pushes over 300K IO/s
    through them at 1ms response time. '
---
<p>I was recently asked by a customer for some help with their new VNX 5500 File + ESXi farm.  This was a greenfield design, and so the customer had gone down a pretty cool route for the networking.</p>
<p>They had a single Extreme Networks Summit switch (10GbE) connected via 10GB to both their hosts and to the VNX.  2 ports on the host were connected to the switch and configured for an active LACP group.  The same was true for the VNX datamovers - 2 ports, active LACP.</p>
<p>Everything on the switch showed 'Up', so the did the array and the hosts.  The array and host were configured per best practices for IP hash load balancing and beacon probing, etc.</p>
<p>However, they had a problem - any time they tried to deploy to the array's datastore store over NFS, or even just browse the datastore, they'd get a pause, and then the datastore would go offline from the ESXi host perspective.  Looking at the vmkernel.log, we saw that the datastore would get marked as All Paths Down.</p>
<p>My first thought was that this was network related.  Usually, stuff going on/off line happens somewhere in the network.  If it wasn't up at all, it wouldn't connect in the first place, and performance would be a totally different issue.</p>
<p>So, I checked jumbo frames first - it looked correct, but just to be sure, we knocked everything back down to 1500 just to be safe.  No dice :(</p>
<p>Next we started looking at the LACP configs on the array, host and switch.  They all looked right at first glance.  But then, when I looked carefully at the config for the LACP trunks from the switch side, they were configured for L2 load balancing.  As astute readers will notice, thats NOT the same as ip hash (which is a L3 method), which we used above.</p>
<p>So, we tore down the LACP group, and rebuilt it with L3 load balancing.  With that, everything came online, browsing was fast, and we were able to deploy VMs.</p>
<p>Moral of the story:  Just because your network team says they followed instructions, doesn't mean they did :)</p>
