---
layout: post
status: publish
published: true
title: VPLEX / vSphere Pathing Recommendation Updates
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 531
wordpress_url: http://www.exaforge.com/?p=531
date: '2014-09-03 09:39:17 -0400'
date_gmt: '2014-09-03 16:39:17 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p><strong>Hot Off The Presses!</strong></p>
<p>I just received word that our current recommendation of pathing policy for VPLEX in VMware environments (Fixed) is changing.  Here's the details until the updated tech book comes out.</p>
<ul style="padding-left: 30px;">
<li>The recommended multipathing setting is <strong>Round Robin</strong> for VPLEX Local, VPLEX Metro (non-cross-connect), and VPLEX Geo. The I/O Limit value should be left at the default setting of <strong>1000</strong>.</li>
<li>For VPLEX Metro cross-connect with VMware, PowerPath/VE is <strong>highly recommended.</strong>
<ul>
<li>PowerPath/VE 5.8 includes the auto-standby feature which allows each ESXi host to automatically prefer to send I/O to its local VPLEX cluster over the remote cluster. The host paths connected to the local VPLEX Cluster will be the active paths whereas those connected to the remote VPLEX Cluster will be the standby paths. [<em>ed. yet ANOTHER reason to use PowerPath/VE!</em>]</li>
<li>For more information on PowerPath/VE and the auto-standby feature, see the support page:<a href="redir.aspx?C=b_9FGqCf1k-20DJ7Cf-sUROJ874nm9EI8gIbfzriiHcegFcLQEp3786YUQ1obSJBdywmn7VI8Ck.&amp;URL=https%3a%2f%2fsupport.emc.com%2fproducts%2f1800_PowerPath-VE-for-VMware">https://support.emc.com/products/1800_PowerPath-VE-for-VMware</a></li>
</ul>
</li>
</ul>
<p>Why did we make this change?  There are three problems with using NMP for VPLEX Metro cross-connect environments:</p>
<ul>
<li>Round-robin path policy for a host connected to both VPLEX clusters will incur extra read and write latency for I/O operations to the remote cluster. Roughly half of the I/O will be local and half will be remote. WAN bandwidth for front-end host traffic will be consumed. Additional VPLEX inter-cluster cache-coherency traffic will be sent between clusters.</li>
<li>Fixed path policy requires a lot of manual administrative work to have all ESXi hosts and all volumes on both clusters to prefer their local cluster. For a handful of hosts and only a few volumes this might be acceptable. But for hundreds of hosts and thousands of volumes this is too onerous.</li>
<li>In addition, should the single preferred path fail for whatever reason, the new path chosen by a host might be at the remote cluster. And it's entirely possible that multiple hosts could by the luck of the draw unfortunately all choose the same new remote director and thus overload that one director. A manual re-balancing of paths would be required at the new cluster, and then when the old cluster is back online, the exercise has to be repeated all over again.</li>
</ul>
