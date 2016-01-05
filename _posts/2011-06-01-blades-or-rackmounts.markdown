---
layout: post
status: publish
published: true
title: Blades or Rackmounts?
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 57
wordpress_url: http://www.exaforge.com/?p=57
date: '2011-06-01 10:36:59 -0400'
date_gmt: '2011-06-01 17:36:59 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 18
  author: Pete
  author_email: peter.gavin@live.com
  author_url: ''
  date: '2012-04-11 12:17:00 -0400'
  date_gmt: '2012-04-11 19:17:00 -0400'
  content: It was a toss-up for us, but ended up going with blades (M1000e enclosure). 
    One of the side benefits that I've grown to appreciate is power protection.  As
    a relatively small organization with really poor power at our facility, is that before
    the blade cluster, I had no good unified structure of UPS units to protect the
    various hosts.  Just a bunch of small UPS units scattered everywhere.  Having
    one 10kva UPS feeding in from 3-phase and powering my blade cluster, switchgear,
    and SAN arrays have helped for managing my power situation here, in planned, and
    unplanned events.
- id: 19
  author: Techno
  author_email: technoroyal9@gmail.com
  author_url: ''
  date: '2012-09-21 12:58:05 -0400'
  date_gmt: '2012-09-21 20:58:05 -0400'
  content: Thanks for nice comprision.
---
<p>If you follow the VMware forums at all, you've seen the discussions about whether to go with a blade server solution for your VMware farm or to a more traditional rackmount solution.  Considerations such as scale, connectivity, performance and redundancy are critical.</p>
<p>There are excellent reasons for going both ways, and some interesting intermediate solutions.</p>
<p><strong>Considerations</strong>:</p>
<ul>
<li>Connectivity/Scaling - You should select a system that meets your growth expectations over the next three years.</li>
<li>Price - I would argue that the second most important piece is the entry &amp; scaling price of the solution.</li>
<li>Performance / Configuration Options - Does the blade or rack system have the connectivity you need?</li>
<li>High Availability - Any solution needs to meet your availability constraints.</li>
</ul>
<p><strong>Scaling</strong>:</p>
<p>Most of the time*, you can stuff more connectivity into a rackmount machine than a blade.  A given 2U server is likely to have 4 or more PCI slots, each of which could potentially provide all the network connectivity you could need.  By comparison, the blade system is unlikely to have more 3 slots, and their connectivity options may be limited or fixed (think of the 'A' fabric on the Dell systems fixed to 1Gb Ethernet).</p>
<p>So from that perspective, I think that the connectivity options on rackmounts win out from a sheer numbers viewpoint.  That being said, there are some downsides.  Most blade server chassis have some form of internal switch or blackplane used for communication between the blades and to the outside world.  This backplane is usually significantly faster than what could be achieved using standard rackmount single hop (via a switch) methods.  As an example, the Cisco UCS chassis provides 1.2Tb of backplane throughput across 8 system blades.  150 GBit should be enough for anyone!</p>
<p><strong>Price:</strong></p>
<p>For me, price is the is clearest differentiator for many users.  Specifically, the entry cost.  Are you planning to buy only 4 hosts over the next three years?  The multi-thousand dollar cost for an empty blade chassis (subject, of course, to your company's discount structure with the vendor) can be a cost that can't be easily amortized.  For those customers, a blade system wouldn't make much sense unless the performance or configurations override the cost hit.</p>
<p><strong>Performance / Connectivity Options</strong>:</p>
<p>This, I believe, is another strong point of blade servers.  For whatever reason, most of the rackmount server manufacturers (with the notable exception of Cisco) have neglected their rackmount systems for IO virtualization features, while providing their blade systems with them.  I don't entirely get why not, but such is life.</p>
<p>Many of the blade server offering out there (Cisco, HP, and a little bit Dell) offer some ability to virtualize the IO.  HP calls this FlexFabric, Cisco does with with the Palo cards and Dell is starting to do it.  Further, there are third party vendors like Xsigo (who I've had great experiences with) and Virtensys (no experience, but a close friend went to work for them, which says something to me).</p>
<p>To me, this is the single most important benefit of blade server technology today.  The ability to just 'create' a NIC or HBA out of thin air by carving a chunk out of your existing connection is incredibly important to future-proofing your environment for whatever comes next.</p>
<p><strong>High Availability</strong></p>
<p>High availability is an interesting concern for blade users.  Many would argue that blade servers are subject to decreased availability because of the number of shared components between the blades, and that all components are subject to the same power supplies.</p>
<p>I have to agree with this argument for the use case of a single blade chassis (say 8 or 16 blades) compared to rack mount servers.  There are indeed more shared components, and the possibility exists, albeit slim, that one could lose the entire chassis an all blades with it.  This isn't the case, clearly, for a rack mount solution.</p>
<p><strong>Recommendations:</strong></p>
<p>You say: "Thanks, Matt, now what do you recommend?"</p>
<p>First, evaluate your growth plans - if you aren't going to exceed 8 hosts, don't bother with the blades - I doubt you'll make up the cost of the chassis overall.  The exception* to this would be if you desperatly need the interconnect performance or virtualized IO that many blades offer.</p>
<p>Next, evaluate your redundancy needs.  If you'd end up having to put everything in 1 chassis (like the 16 slot Dell M1000e), I'd avoid the blades, just for the improved availability.</p>
<p>If neither of the above - absolutely, go with the blades.</p>
<p>I realize some of this is controversial - please comment!</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h6>* blah blah blah - yes there are exceptions to every rule :)</h6>
