---
layout: post
status: publish
published: true
title: 'Quick Post: Launching Custom Actions on LogInsight Alerts'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 503
wordpress_url: http://www.exaforge.com/?p=503
date: '2014-06-09 13:04:42 -0400'
date_gmt: '2014-06-09 20:04:42 -0400'
categories:
- Uncategorized
tags:
- Python
- VMware
- log
- insight
- loginsight
- script
- module
comments: []
---
<p>Right now, LogInsight doesn't have the ability to launch a custom action on an alert - its limited to email alerts only.</p>
<p>For a recent customer issue (repeated, unexplained APDs), we needed the ability to react faster than manual email checking, and the method of having the email sent, then checking for it programmatically and performing an action was going to be too slow and fragile.</p>
<p>So I looked into what it would take to be more direct.  I asked<a href="http://blogs.vmware.com/management/author/jonherlocker"> Jon Herlocker</a> from the LogInisght team what could be done, and he pointed out to mean that LogInsight writes all of its alerts to an alert.log file.  Even better, they are in JSON format, making them easily parsed:</p>
<pre><span id="s-1" class="sBrace structure-1" style="color: #666666;">{</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span id="s-2" class="sObjectK" style="font-weight: bold; color: #36393b;">"Data"</span><span id="s-3" class="sColon">:</span><span id="s-4" class="sBrace structure-2" style="color: #666666;">{</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-5" class="sObjectK" style="font-weight: bold; color: #36393b;">"Name"</span><span id="s-6" class="sColon">:</span><span id="s-7" class="sObjectV" style="color: #000000;">"All Paths Down!!!"</span><span id="s-8" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-9" class="sObjectK" style="font-weight: bold; color: #36393b;">"Data"</span><span id="s-10" class="sColon">:</span><span id="s-11" class="sBrace structure-3" style="color: #666666;">{</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-12" class="sObjectK" style="font-weight: bold; color: #36393b;">"Count"</span><span id="s-13" class="sColon">:</span><span id="s-14" class="sObjectV" style="color: #000000;">2</span><span id="s-15" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-16" class="sObjectK" style="font-weight: bold; color: #36393b;">"Fields"</span><span id="s-17" class="sColon">:</span><span id="s-18" class="sBrace structure-4" style="color: #666666;">{</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-22" class="sBrace structure-4" style="color: #666666;">}</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-23" class="sBrace structure-3" style="color: #666666;">}</span><span id="s-24" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-25" class="sObjectK" style="font-weight: bold; color: #36393b;">"HitCount"</span><span id="s-26" class="sColon">:</span><span id="s-27" class="sObjectV" style="color: #000000;">1</span><span id="s-28" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-29" class="sObjectK" style="font-weight: bold; color: #36393b;">"Url"</span><span id="s-30" class="sColon">:</span><span id="s-31" class="sObjectV" style="color: #000000;">"http://&lt;snip&gt;"</span><span id="s-32" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-33" class="sObjectK" style="font-weight: bold; color: #36393b;">"Info"</span><span id="s-34" class="sColon">:</span><span id="s-35" class="sObjectV" style="color: #000000;">"2014-06-09T16:48:46.311Z&lt;snip&gt; vobd: [APDCorrelator] 3012029821721us: [esx.problem.storage.apd.start] Device or filesystem with identifier [0e9de197-9b49ee96] has entered the All Paths Down state."</span><span id="s-36" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span style="color: #000000;">   </span><span id="s-37" class="sObjectK" style="font-weight: bold; color: #36393b;">"EditUrl"</span><span id="s-38" class="sColon">:</span><span id="s-39" class="sObjectV" style="color: #000000;">"http://&lt;snip&gt;"</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span id="s-40" class="sBrace structure-2" style="color: #666666;">}</span><span id="s-41" class="sComma">,</span><br style="color: #000000;" /><span style="color: #000000;">   </span><span id="s-42" class="sObjectK" style="font-weight: bold; color: #36393b;">"Timestamp"</span><span id="s-43" class="sColon">:</span><span id="s-44" class="sObjectV" style="color: #000000;">"2014-04-14T20:10:39.585Z"</span><br style="color: #000000;" /><span id="s-45" class="sBrace structure-1" style="color: #666666;">}</span></pre>
<p>Thats pretty handy!  As a result, I wrote a quick python script that uses the builtin json module as well as <a href="https://pypi.python.org/pypi/tailer3/0.3">a module I found on PyPI called 'tailer3'</a> to constantly watch that file, parse the new lines (with some checks for exceptional conditions), and, if needed, lookup the appropriate data mover for the APD, then do something with it.</p>
<p>I hope this helps!</p>
<p>As usual, its <a href="https://github.com/mcowger/LogInsightScriptRunner">available on my GitHub</a></p>
<p>&nbsp;</p>
