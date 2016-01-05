---
layout: post
status: publish
published: true
title: VNX to vCenter Operations Adapter
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 141
wordpress_url: http://www.exaforge.com/?p=141
date: '2011-09-29 18:02:00 -0400'
date_gmt: '2011-09-30 02:02:00 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 43
  author: David
  author_email: david.manconi@gmail.com
  author_url: ''
  date: '2011-09-30 03:06:00 -0400'
  date_gmt: '2011-09-30 10:06:00 -0400'
  content: |-
    Hi

    excellent scripts and idea. Can you confirm what version of VCOPS you are using please. The enterprise product or the virtual appliance?

    Cheers
- id: 44
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-09-30 19:14:00 -0400'
  date_gmt: '2011-10-01 02:14:00 -0400'
  content: |-
    Thanks David,

    This is vCops 1.01 Enterprise.  The standard version doesn't support external adapters.
- id: 45
  author: Adam
  author_email: adam.savage@monster.com
  author_url: ''
  date: '2013-01-22 20:11:08 -0500'
  date_gmt: '2013-01-23 04:11:08 -0500'
  content: Can you tell me if this will work with vCenter Operations Manager 5.6?
    Also will it work with Clariion CX4-960s? I will test it out and see but wanted
    to get some insight first :)
- id: 46
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-01-22 20:20:44 -0500'
  date_gmt: '2013-01-23 04:20:44 -0500'
  content: "It works with 5.6 just fine.  \n\nI don't know that it's ever been tested
    with CX4, but it should if you are on a recent FLARE"
- id: 47
  author: Adam
  author_email: adam.savage@monster.com
  author_url: ''
  date: '2013-01-22 20:22:51 -0500'
  date_gmt: '2013-01-23 04:22:51 -0500'
  content: Perfect - thanks! I will try it out and let you know.
---
<p>Hi Everyone,</p>
<p>I spent some time this week working on a demonstration of getting VNX block performance and configuration data into vCenter Operations for a customer.&nbsp; This scripts are very similar in function to the scripts that Clinton Kitson (fellow vSpecialist) wrote for the Hands On Labs at VMworld 2011 in Las Vegas.&nbsp; Mine are a little different in the following ways:</p>
<ul>
<li>These scripts measure and report block performance only</li>
<li>These scripts pull in every metric that navicli can pull in</li>
<li>These scripts do *not* use Hyperic or any temporary files to post the data - its directly sucked in from memory</li>
<li>These are entirely in PowerShell</li>
</ul>
<p>There are some things to note about these:</p>
<ul>
<li><strong>There is no support for these.</strong>&nbsp; I will try to help you out if you have troubles, but these are NOT an official EMC product, just a demonstration of something cool.</li>
<li>These are currently alpha quality at best - <strong>you are on your own if you rely on them for anything important.</strong></li>
<li>The script supports only 1 array at a time.&nbsp; I intend to change that, but for now thats the deal.&nbsp; If you need to collect stats for more than 1 array, run the scripts multiple times.</li>
<li>Eventually these will be deprecated, whenever VMware comes out with a real adapter and not this hack :)</li>
</ul>
<p>So, instructions:</p>
<p>Install NaviCLI from PowerLink, and copy naviseccli.exe to the same directory you put this script.&nbsp; Ensure that statisics collection is enabled on your array.</p>
<p>Execute the with the following parameters:</p>
<pre>.vnxvcops.ps1 -username &lt;usernameforarray&gt; -password XXXXXX -scope 0 -spaip &lt;IP of SPA&gt; -spbip &lt;IP of SPB&gt; -interval &lt;Post interval in minutes&gt; -URL http://&lt;vcopsserverhostname&gt;/HttpPostAdapter/OpenAPIServlet</pre>
<p>When the script executes, it will display some progress information during the collection and posting process, and a quick output on every interval:</p>
<pre>9/29/2011 8:38:22 PM : Post Completed - Sleeping 5 minutes before next post
9/29/2011 8:43:32 PM : Post Completed - Sleeping 5 minutes before next post
9/29/2011 8:48:41 PM : Post Completed - Sleeping 5 minutes before next post</pre>
<p>What can you do in vCops with the data?&nbsp; How about this:</p>
<p><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-29-at-6-28-21-pm.png"><img class="aligncenter size-full wp-image-143" title="Screen Shot 2011-09-29 at 6.28.21 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-29-at-6-28-21-pm.png" alt="" width="1023" height="598" /></a><a href="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-29-at-6-28-33-pm.png"><img class="aligncenter size-full wp-image-144" title="Screen Shot 2011-09-29 at 6.28.33 PM" src="http://www.exaforge.com/images/2011/09/screen-shot-2011-09-29-at-6-28-33-pm.png" alt="" width="1032" height="475" /></a>You can get the latest copy of the script always from here:</p>
<p><a href="http://code.google.com/p/vnxvcopsadapter/source/browse/trunk/vnxvcops.ps1">https://community.emc.com/thread/130486</a></p>
<p>To Do:</p>
<ol>
<li>Better error checking</li>
<li>LUN/Type Aggregations</li>
<li>Log to Event Log instead</li>
<li>Run as a service?</li>
<li>Support configuration file with multiple arrays</li>
<li>Support reporting on VNX File (NFS/CIFS) statistics</li>
<li>Other?</li>
</ol>
<p>Let me know if it works for you and other new features/bug fixes.</p>
