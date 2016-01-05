---
layout: post
status: publish
published: true
title: ALUA active arrays
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 67
wordpress_url: http://www.exaforge.com/?p=67
date: '2011-07-03 11:13:18 -0400'
date_gmt: '2011-07-03 18:13:18 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 20
  author: Guy
  author_email: guy@bk.se
  author_url: ''
  date: '2011-09-21 19:21:00 -0400'
  date_gmt: '2011-09-21 19:21:00 -0400'
  content: excellent!
- id: 21
  author: Alexanderthoma
  author_email: alexanderthoma@gmx.de
  author_url: ''
  date: '2011-10-26 01:35:00 -0400'
  date_gmt: '2011-10-26 08:35:00 -0400'
  content: Honestly ... I do not see any reason why I need ALUA on a Symmetrix device.
    I can use RR without it. Even if you have a VNX attached to the same cluster,
    it gets its own policy and can use ALUA accordingly.
- id: 22
  author: Stephane G
  author_email: sgrandgeorge@vmware.com
  author_url: ''
  date: '2011-10-26 03:44:00 -0400'
  date_gmt: '2011-10-26 10:44:00 -0400'
  content: to second Alexanderthoma, There is no need of ALUA for active/active array
    as defined all the path are equal. now about esx, it will alway's only use , at
    any point of time, one path being active/active or active/passive. that is about
    how we detect the path and detect their characteristic.now your are mixing that
    with how we select the path dynamically. up until introduction of round robin
    in vmware esx would only change path in case of failure or non active path.spo
    what does round robin introduce is mechanism for failing over not only for failure
    handling but as well to optimise performance. so after a certain number of i/o
    it will move to another path selecting path one after an other.this would have
    been an issue with Acrtive/passive array as it ius very wel explained in your
    earticle, but for active/active array that is no problemas mentioned all path
    are equal.hence ALUA is only for active/passive array.
- id: 23
  author: Madan Cheemakurthi
  author_email: addressmadan@gmail.com
  author_url: ''
  date: '2013-01-01 02:41:04 -0500'
  date_gmt: '2013-01-01 10:41:04 -0500'
  content: |-
    My question may be very naive, but  I would like to know, why an application would directly communicate with a controller in an Array? As far as I know, there can be multiple controllers in an array and it is total abstraction for the end user or end application as to which controller it is talking to. All that the application would know is it is communicating with a storage array and the storage array has internal logic to use which controller for a specific purpose.
    Please let me know if I 'am missing anything here.
---
<p>There was an interesting discussion the other week on Twitter about ALUA arrays and why would you need/want ALUA support on an array that was truly symmetric.  I'd like to tackle that one, and make the suggestion that even a truly symmetric array ought to support ALUA notation, solely for management reasons.</p>
<p><strong>ALUA</strong></p>
<p>But first, what is ALUA?  To be extra clear, ALUA is *not* a vSphere or VMware feature.  It stands for Asymmetric Logical Unit Access.  Lets break that down:</p>
<ul>
<li>Asymmetric:  Unequal.</li>
<li>Logical Unit: A more proper name for what many people call a 'LUN'.  LUN stands for Logical Unit Number, which directly refers only to the umerical identifier of a volume of storage.  The device itself is more accurately called a Logical Unit (LU) or volume.</li>
<li>Access: A way of using a resource.</li>
</ul>
<p>Put that all together and you get that ALUA refers to unequal ways of accessing a logical unit.</p>
<p><strong>Real Arrays</strong></p>
<p>Many (most?) storage arrays have some level of redundancy built on.  Whether that is simply multiple ethernet ports to allow for switch, cable and NIC failures (think of an iomaga IX4 style prosumer array), a pair of active / passive controllers where one can take over for the other (think of the old HP MSA1000 arrays), a pair of controllers where each one processes data live but to a limited extent (think of the EMC Clariion and VNX line, HP EVA, etc) or a design where all aspects of the system of fully active and redundant at all times (EMC Symmetrix, HDS VSP, 3PAR, etc).  All of this comes down to providing multiple ways (paths) for a host to access its data so that it can avoid the downtime from a complete failure.</p>
<p>Now, as I mentioned above, not all methods are equal.  Certainly the Symmetrix has better redundancy and reliability than the IX4 does - it has multiple controllers (think of them as highly specialized storage computers) that can access the data.  But how does something like the Symmetrix compare to a VNX?  Both of them support multiple controllers (often called heads, SPs, nodes, etc by various vendors), so are they equal?  In a word: no.  The difference comes partly in the ability to scale, but mostly in their ability to access the data.</p>
<p>In all active / passive arrays, only one controller is active at a time.  It processes all incoming IO requests and manages all of the cache coherency, etc.  This is certainly an easier way to do it, however its also wasteful, as you have a second controller just sitting there doing nothing.  Wouldn't it be cool if you could get it to help out?  Indeed, thats what most array vendors have started to do.  As an example, the EMC VNX (and plenty of others, we aren't unique in this) allows both controllers to be active processing data simultaneous, and act as failure partners for each other.  However, any given logical unit (volume, LUN) is 'owned' by exactly one of the controllers at any given time.  This allows for improved performance (because you can split the workloads across controllers), high availability (because the pair act as failover partners) and is pretty great.</p>
<p><strong>Avoiding Path Thrashing</strong></p>
<p>But what happens if you send an IO request to the non-'owning' controller of a given LU?  Well, that varies by array.  Lower end and some older arrays would simply reject the IO, thus causing problems with your application.  The next level for many arrays was not to reject the IO, but to hold the request in memory, internally change the ownership of that LU to the new controller, then process the IO.  Doing so is usually referred to a a LUN 'trespass'.  This worked fine, however, but came at the cost of a delayed processing of an IO (the delay varies, but is often on the order or 300-1000 ms).  Start doing this trespass action back and forth between the controllers, and your IO performance clearly goes through the floor and you end up in a situation called 'path thrashing'. The last method, now the most common and that used by EMC VNX (and others) is to simply invisibly and passively transfer that IO destined for the 'wrong' controller off to the correct controller for processing, and then to do the same on the response.  This avoids the nasty path thrashing problem I mentioned earlier, but requires a high speed interconnect between the controllers.</p>
<p>But wait, you say.  That sounds like it takes an extra hop.  Indeed it does, making access to a given LU via the non-owning (aka non-preferred) controller asymmetric compared to access directly.  Its asymmetric access to the logical unit, or ALUA.</p>
<p><strong>Avoiding Guessing</strong></p>
<p>So how can an operating system avoid paying this asymmetric tax?  There are two primary ways:</p>
<ol>
<li>Use a dedicated multipathing driver that knows (somehow) which is the preferred controller, and chooses to use that one whenever possible.  This is exactly what drivers like EMC PowerPath do - they know the best controller path, and use that info (along with a whole ton of other metrics).</li>
<li>Use a standard method for the array controller to send a hint back to the operating system  about which paths are the best (or preferred).  Indeed, such a standard exists, and its alled the ALUA standard.  Arrays supporting this standard, when their are queried about available LU's down a given path not only send the volume/LU information, but also information about whether that given path is the best way to get to that LU.</li>
</ol>
<p><strong>Symmetric Arrays and ALUA</strong></p>
<p>Now that you've got a solid understanding of the problems that ALUA solves, lets talk about a Symmetric array.  A truly symmtric array (like the EMC Symmetrix, 3PAR T &amp; F class, Hitachi VSP, etc) has no need itself for such information.  All paths to all volumes are 100% equivalent, hence the term symmetric.  So there would be no need for such an array to even support ALUA semantics, right?  The array doesn't care, so the host shouldn't either.</p>
<p>I would argue that that it should.  Let me set the stage for an example:</p>
<p>You've got a big honking 32-node vSphere 4.1 cluster thats going to be running mixed Tier 1 and Tier 2/3 workoads.  You go out and buy a  Symmetrix V-MAX array for the Tier 1 and a VNX for the tier 2.   You get everything hooked up, zoned in, etc.</p>
<p>Now, what if your VNX supported ALUA but your Symmetrix didn't.  VMware would automatically know which controllers to use for anything on the VNX array (your Tier 2 workloads) but wouldn't have any idea about your Symm.  To be safe, it would have to default to seeing all the paths, but only using one.  Thats inefficient.  If it did support ALUA (which it does) it can go tell VMware ESX that all paths are preferred, and VMware will automatically make use of them as needed.</p>
<p>As a result, because the Symmetrix supports ALUA, you can simply set a default policy of round-robin access for all paths to all LUs, and the ALUA reporting into VMware from the array will allow VMware to do the right thing at all times, which no manual management at all.</p>
<p><strong>Summary</strong></p>
<p>My argument about ALUA for symmetric arrays isn't about performance, its about management.  Its simply easier for you to manage access to, and performance from, a true symmetric array if it supports ALUA reporting.</p>
