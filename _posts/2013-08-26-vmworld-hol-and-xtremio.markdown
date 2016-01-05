---
layout: post
status: publish
published: true
title: VMworld HoL and XtremIO
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 388
wordpress_url: http://www.exaforge.com/?p=388
date: '2013-08-26 23:39:27 -0400'
date_gmt: '2013-08-26 23:39:27 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>In case you haven't noticed, this years Hands On Labs at VMworld are bigger than ever - they have something on the order of 450 seats, plus space for another 200 BYOD sessions.  So basically, 700 seats.  Combine that with the fact that they keep nearly 300 labs prepoluated for performance reason, and you have about 10,000 VMs running at a given time.  </p>
<p>When the VMworld HoL team came to me asking for help and gear, I knew right away that the XtremIO platform was exactly what they needed.  Designed for thousands of VMs with redundant data (vCD and and VDI workloads), it seemed perfect. </p>
<p>So, we shipped them 4 XBricks, each with 7TB addressable storage (which turns into about 60TB useable after their 5.2:1 dedupe ratios).  They spun them up, and found that the performance was just rock solid.  </p>
<p>I went over to say hi to them today and check on how things are going. The team that runs it (the internal VMware OneCloud team) was very happy with the performance so far. Even at full load, the arrays are doing between 12,000 and 50,000 IO/s each, which is less than 20% of their capability.  Further, all the response times are less than 500usec (0.5ms).  </p>
<p>Truly impressive.  If you go take a lab, go to the back of the room and ask for a tour of the monitoring systems. </p>
<p>As the team told me, "these arrays are bored":<br />
<br /><a href="http://www.exaforge.com/images/2013/08/20130826-163947.jpg"><img src="http://www.exaforge.com/images/2013/08/20130826-163947.jpg" alt="20130826-163947.jpg" class="alignnone size-full" /></a></p>
<p>"This is the hardest they have worked all day" - during a nightly UNMAP process:</p>
<p><a href="http://www.exaforge.com/images/2013/08/20130826-163938.jpg"><img src="http://www.exaforge.com/images/2013/08/20130826-163938.jpg" alt="20130826-163938.jpg" class="alignnone size-full" /></a><br /</p>
