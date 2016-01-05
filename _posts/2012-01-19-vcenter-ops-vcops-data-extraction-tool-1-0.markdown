---
layout: post
status: publish
published: true
title: vCenter Ops (vCops) Data Extraction Tool 1.0
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 231
wordpress_url: http://www.exaforge.com/?p=231
date: '2012-01-19 09:00:00 -0500'
date_gmt: '2012-01-19 17:00:00 -0500'
categories:
- Uncategorized
tags:
- Python
- vCenter Operations
- vCops
comments:
- id: 60
  author: 'Technology Short Take #20 &#8211; blog.scottlowe.org'
  author_email: ''
  author_url: http://news.deadmau5wallpaper.info/technology-short-take-20-blog-scottlowe-org/
  date: '2012-01-20 17:24:12 -0500'
  date_gmt: '2012-01-21 01:24:12 -0500'
  content: '[...] Using vCenter Operations? You competence be meddlesome in this apparatus
    combined by Matt Cowger called the vCenter Operations Data Extraction Tool. [...] '
- id: 61
  author: ewalk153
  author_email: ''
  author_url: http://twitter.com/ewalk153
  date: '2012-01-22 01:47:00 -0500'
  date_gmt: '2012-01-22 09:47:00 -0500'
  content: Why not push it to github?
- id: 62
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2012-01-22 20:23:00 -0500'
  date_gmt: '2012-01-23 04:23:00 -0500'
  content: Because I dislike git (I know! heresy!)
- id: 63
  author: Vince
  author_email: vmeoc@vmware.com
  author_url: ''
  date: '2012-04-25 08:53:00 -0400'
  date_gmt: '2012-04-25 15:53:00 -0400'
  content: "Hi MAtt,\n\nNice tool. Does it work on the vCenter Operations vApp or
    just on the standalone installation? "
- id: 64
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2012-04-25 08:55:00 -0400'
  date_gmt: '2012-04-25 15:55:00 -0400'
  content: Either one.  But it does require the vCenter Operations Enterprise license.
- id: 65
  author: Stace Hipperson (@stacehipperson)
  author_email: stacehipperson@twitter.example.com
  author_url: http://twitter.com/stacehipperson
  date: '2012-07-25 08:53:42 -0400'
  date_gmt: '2012-07-25 16:53:42 -0400'
  content: Nice work. Is the vOps DB schema easy to navigate/make sense of? I am thinking
    of trying to hook into it and visualise what they have on top of vCenter stuff.
- id: 66
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2012-07-25 09:30:17 -0400'
  date_gmt: '2012-07-25 17:30:17 -0400'
  content: Its not unreasonable, but its not trivial at all.  Look at this blog post
    (http://www.exaforge.com/2012/01/11/getting-the-adapterkindkey-resourcekindkey-for-every-vcops-resource/)
    and you'll see that its pretty reasonably normalized, so be prepared for plenty
    of LEFT JOINs.
- id: 67
  author: Stace Hipperson (@stacehipperson)
  author_email: stacehipperson@twitter.example.com
  author_url: http://twitter.com/stacehipperson
  date: '2012-07-25 12:45:26 -0400'
  date_gmt: '2012-07-25 20:45:26 -0400'
  content: Yep, looks relatively sensible. Thanks, that's given me a good start! When
    is your secret project launching or is this one of those always going to be secret
    things?
- id: 2770
  author: michael grove
  author_email: jmgroveandass@gmail.com
  author_url: ''
  date: '2014-03-06 19:25:55 -0500'
  date_gmt: '2014-03-06 19:25:55 -0500'
  content: Great information, is the download still available?
- id: 10282
  author: Karlan Talkington
  author_email: kbtalkin@yahoo.com
  author_url: ''
  date: '2014-06-05 15:34:06 -0400'
  date_gmt: '2014-06-05 22:34:06 -0400'
  content: I also would like to know if the download is still available
- id: 10284
  author: Karlan Talkington
  author_email: kbtalkin@yahoo.com
  author_url: ''
  date: '2014-06-05 15:36:17 -0400'
  date_gmt: '2014-06-05 22:36:17 -0400'
  content: 'Looks like it was moved to GIT: https://github.com/mcowger/vcopsextract'
---
<p>This morning I'm pleased to release the vCops Data Extraction Tool 1.0</p>
<p>Have you ever wanted to extract raw data from your vCops installation.  By raw I mean really raw - each and every timestamped value for each metric and resource?  I had such a need recently for an upcoming secret project, so I decided to write such a tool  Because I wanted this to be cross platform, my previous work with <a href="http://velemental.com/" target="_blank">Clint Kitson based on Powershell </a>was out.  So, my backup language is always Python, and I chose that.</p>
<p>I'm posting the code here under a Creative Commons license for everyone to use as needed.  As an example of how to run it (it does include help if you need it):</p>
<h1 style="padding-left:30px;"><span style="font-family:'courier new', courier;font-size:small;">python vcopsextract.py --oraclehome /u01/app/oracle/product/11.2.0/xe/ --sqlplus /u01/app/oracle/product/11.2.0/xe/bin/ --user vcops11 --password usr4vcops --oracleSID XE --oraclehost localhost --outputfile data.out --debugfile debug.out --vcopsurl http://localhost/HttpPostAdapter/OpenAPIServlet --days 1</span></h1>
<p>Most of this just specifies the Oracle database (which I need to connect to and query for the resource metric names, etc), the output files, the OpenHTTPAdapter location and the number of days of data to retrieve.  I highly recommend restricting the number of days of data to retrieve to a reasonable number like 7, as the data generated by this tool is quite significant.  As an example, in my small vCenter + VNX demo environment with about 566 resources, 7 days of data was well over 8GB of text files.</p>
<p>When it executes it looks something like this:</p>
<p><img style="float:left;" src="http://www.exaforge.com/images/2012/01/screen-shot-2012-01-18-at-4-14-07-pm.png" alt="vCops Extractor Run" width="1069" height="212" /></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>Note the pretty progress bars :)</p>
<p>When its complete, you'll see in the datafile:</p>
<p><img src="http://www.exaforge.com/images/2012/01/screen-shot-2012-01-18-at-4-14-32-pm.png" alt="" width="1104" height="307" /></p>
<p>The first like is a token indicating when this file was created (milliseconds since Epoch).  The second line is the vCops-style information about the resource, including its name, Adapter Type, Identifiers, etc.</p>
<p>The following lines are the metrics for each individual metric with:</p>
<p style="padding-left:30px;"><span style="font-family:'courier new', courier;">metricname,alarmlevel,&lt;blank&gt;,timestamp,value,&lt;blank&gt;</span></p>
<p>This format is intended to be easily reimported to vCops later.</p>
<p>TODOs:</p>
<ul>
<li>Enable gzip'd output</li>
<li>Enable selection only of certain resources</li>
<li>Enable selection only of certain metrics</li>
<li>Other?</li>
</ul>
<p><a href="http://www.cowger.us/vcopsextract.tgz">Download Here</a></p>
