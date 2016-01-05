---
layout: post
status: publish
published: true
title: EMC DART-based NAS Systems & MacOS Lion BUG
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 83
wordpress_url: http://www.exaforge.com/?p=83
date: '2011-07-22 09:53:34 -0400'
date_gmt: '2011-07-22 16:53:34 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>I dont normally blog about NAS stuff or Mac stuff, but this is pretty important.</p>
<p>If you run EMC's DART-based NAS platforms (NS-series, VNX-series) and you have MacOS Lion users, you are at risk for some pretty serious bugs, up to including outages.  You <strong>need</strong> to upgrade to one of these DART version:</p>
<p>5.6.51.314 - 320<br />
6.0.40.806 - 811<br />
6.0.41.3 or higher<br />
7.0.12.100 - 101<br />
7.0.13.x or higher (including 7.0.14.x)</p>
<p>If you don't, here's what you get:</p>
<p>"The issue we are seeing is caused when an end user running the new MAC OS version attempts to access a NAS share, this causes the<strong> Data Mover to panic</strong>.  If the user continues to try to access the share, the surviving DM will also panic which can cause an outage. If the panic occurs on a Standby DM it <strong>will cause a reboot which will cause a full outage for all users</strong>, not just the MAC OS users."</p>
<p>The back story is that we saw this bug while Lion was in beta and reported it to Apple back in March.  We asked they fix it by GA, but they refused :(.  So, here we are.</p>
<p>Consider this fair warning!</p>
