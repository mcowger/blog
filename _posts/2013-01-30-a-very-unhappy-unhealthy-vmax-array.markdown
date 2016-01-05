---
layout: post
status: publish
published: true
title: A very unhappy, unhealthy VMAX array
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 331
wordpress_url: http://www.exaforge.com/?p=331
date: '2013-01-30 14:12:52 -0500'
date_gmt: '2013-01-30 22:12:52 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 112
  author: Dave Silvestri
  author_email: dave.silvestri@outlook.com
  author_url: ''
  date: '2013-01-30 15:28:07 -0500'
  date_gmt: '2013-01-30 23:28:07 -0500'
  content: |-
    Very nice... if you can get the client to agree to look at FAST and Virtual Pools (and thus FAST VP) you should get another heat map from the time that it's turned on, and just watch the IO level out and flow on the backend.

    A little more manual balancing on the front end to occur mind you... :)
- id: 113
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-30 15:53:59 -0500'
  date_gmt: '2013-01-30 23:53:59 -0500'
  content: Thats the plan :)
- id: 1251
  author: ulimited hosting
  author_email: rfksrb@gmail.com
  author_url: http://www.beckett.com/users/MariellayqKoirnubrad
  date: '2013-08-05 15:03:42 -0400'
  date_gmt: '2013-08-05 15:03:42 -0400'
  content: Hmm is anyone else encountering problems with the pictures on this blog
    loading? I'm trying to find out if its a problem on my end or if it's the blog.
    Any responses would be greatly appreciated.|
- id: 1269
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-08-27 00:35:51 -0400'
  date_gmt: '2013-08-27 00:35:51 -0400'
  content: I just checked from a virgin host, and it's working.   The above is a YouTube
    embed, though, not a image.
---
<p>http://www.youtube.com/watch?v=dVEavmUr2HI&amp;feature=youtu.be</p>
<p>This is what happens, children, when you think you are smarter than the array auto-tiering software and force all your LUNs onto specific disks. You end up with terrible balance and bad performance.</p>
<p>Quick Legend: This is a timelapse of 24 hours in the life of the array.  The bottom rows (the small squares) are the individual disks, with the hot upper left section being all SATA drives, the rest are FC drives.  Clearly a pretty strong imbalance.  The larger squares above that are the actual CPUs that control the data flow.</p>
<p>&nbsp;</p>
