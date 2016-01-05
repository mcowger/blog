---
layout: post
status: publish
published: true
title: 'Quick Post: New Favorite esxcli update commands'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 377
wordpress_url: http://www.exaforge.com/?p=377
date: '2013-07-04 02:35:43 -0400'
date_gmt: '2013-07-04 02:35:43 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>I was updating my lab this morning, and needed to easily update the machines to the latest version of ESXi code without VUM.  Here's a handy one:</p>
<pre>esxcli software profile update -d http://hostupdate.vmware.com/software/VUM/PRODUCTION/main/vmw-depot-index.xml -p `esxcli software sources profile list -d http://hostupdate.vmware.com/software/VUM/PRODUCTION/main/vmw-depot-index.xml | grep "ESXi-5.1.0-2013" | grep no-tools | sort | tail -1 | awk '{print $1}'`</pre>
<p>Also, to install the latest Brocade CNA drivers:</p>
<pre>esxcli software vib install -d "http://www.brocade.com/downloads/documents/vibsdepot/CurrentRelease/BCD-depot-index.xml"</pre>
