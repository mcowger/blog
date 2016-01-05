---
layout: post
status: publish
published: true
title: VMware VDP powered by Avamar
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 255
wordpress_url: http://www.exaforge.com/?p=255
date: '2012-08-29 00:11:22 -0400'
date_gmt: '2012-08-29 08:11:22 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>There sure is a ton of excitement around VDP – VMware’s replacement for VDR, but this time powered by the pretty awesome engine that is Avamar. Others have done really great posts about the value, etc, so I won’t repeat that here. What I would like to do is talk about some of the technical aspects of how it works and what its limited to…</p>
<p>Because VDP is now using Avamar in the background, existing users of Avamar should find its method of operations pretty familiar. If you haven’t seen it, its done something like this:</p>
<ul>
<li>Perform a snapshot of the relevant VM</li>
<li>Attach base disk to VDP agent VM (yes, this is a real VMware agent VM, so it won’t ever get vMotioned, etc)</li>
<li>Using the CBT data, backup the relevant parts of the disk</li>
<li>Detach disk from agent and remove snapshot</li>
</ul>
<p>As a result, all the work of identifying the changed information is offloaded to the hypervisor (in the form of CDP) and all the working of copying that data is offloaded to the agent – the client never has to be involved!</p>
<p>So how is this limited / licensed / nitty gritty?</p>
<p>You can protect 100 VMs per appliance, and guard 2TB of data (AFTER deduplication – which at Avamar’s common 20:1 rates is A LOT). You can run 8 concurrent jobs too, and it works with RDMs (vRDMs only)! So, this is pretty reasonable scaling for small businesses, and possibly for indidivudal sites of some smaller medium businesses. Whats REALLY cool is that if you decide you really like Avamar but want to scale it bigger, there’s a clean upgrade path as well.</p>
<p>So fully inline dedupe thats only limited in performance by your CPU that can be upgraded to a full product and is free to anyone with Essentials Plus licensing – sweet!</p>
