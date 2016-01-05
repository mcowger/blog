---
layout: post
status: publish
published: true
title: Is NSX Really New?
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 400
wordpress_url: http://www.exaforge.com/?p=400
date: '2013-09-04 15:55:21 -0400'
date_gmt: '2013-09-04 15:55:21 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>I had an interesting request from a colleague recently.  They had a customer suggesting that NSX was really nothing new, and could be replicated more cheaply.  In the customer's words:</p>
<blockquote><p>"<span style="font-size: small;">NSX is a suite of tools (not marketed that way, but it is). OpenVSwitch is a single tool. With IPTables, StrongSwan, OpenVPN and OpenVSwitch as a collective suite of tools you can get complete NSX functionality for $0 on Linux.</span>"</p></blockquote>
<p>As you might expect, I have a few thoughts on this.  <span style="font-size: small;">The value of a suite of products is greater than the sum of its parts.  Is it possible to get similar functionality for a narrow set of requirements using iptables, OpenVPN, OpenvSwitch, etc?  Sure.  Is the cost of that $0?  Absolutely not.    Consider the following that NSX offers (as a suite):</p>
<p></span></p>
<ul>
<li><span style="font-size: small;"> Full support from a vendor (non trivial, includes QA testing, and multiple experts to call when things go wrong).  Not something a home grown solution can offer.</span></li>
<li><span style="font-size: small;"> Full support for a huge range of operational models.  Not something a Linux-only solution can offer (as much as I love Linux and detest Windows, Windows does exist).</span></li>
<li><span style="font-size: small;"> Full REST API to integrate into a larger workflow (which is half the point of network virtualization, no?) - could be built by a lone guy, but now he's responsible for a huge dev project.</span></li>
<li><span style="font-size: small;"> Community and Vendor Ecosystem.  Want your stuff to terminate into the real world?  Arista, Broadcom, Brocade, etc can all help with that with NSX.  Homegrown?  Maybe - maybe not.</span></li>
</ul>
<p><span style="font-size: small;"> Time costs money - its not free.  By the time you built out the required software, made it resilient, added a good GUI, built integration into the common products out there, added a API and built a partner ecosystem, you would have built a company worth about $1B.  How do I know?  Because someone did - Martin Casado, and that company was Nicira - now owned by VMware and the basis for VMware NSX.</span></p>
