---
layout: post
status: publish
published: true
title: Things I've Learned About Pebble
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 337
wordpress_url: http://www.exaforge.com/?p=337
date: '2013-02-01 20:32:00 -0500'
date_gmt: '2013-02-02 04:32:00 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 114
  author: SiON42X
  author_email: Sion42x@gmail.com
  author_url: ''
  date: '2013-02-01 20:53:20 -0500'
  date_gmt: '2013-02-02 04:53:20 -0500'
  content: Settings -&gt; bluetooth -&gt; then hit the action button over Bluetooth
    ON. It turns bluetooth off and says airplane mode.
- id: 115
  author: Hexxeh
  author_email: hexxeh@hexxeh.net
  author_url: http://gravatar.com/hexxeh
  date: '2013-02-02 05:45:21 -0500'
  date_gmt: '2013-02-02 13:45:21 -0500'
  content: 'Hey, I''ve been doing some poking around in this too. You should join
    #pebble on Freenode. I''m thinking of setting up a wiki to house all this info.
    I''m looking forward to documenting enough of the protocol such that you can pair
    Pebble with anything and write a counterpart app.'
- id: 116
  author: RaYmAn (@DroidRay)
  author_email: DroidRay@twitter.example.com
  author_url: http://twitter.com/DroidRay
  date: '2013-02-02 09:12:25 -0500'
  date_gmt: '2013-02-02 17:12:25 -0500'
  content: |-
    As Hexxeh says, come join us on IRC in #pebble :).
    I've also been doing some similar investigations with my findings available on my personal wiki at http://wiki.skumler.net/wiki/Pebble

    We should probably find a sane place to collect all our common findings
- id: 118
  author: Here&#8217;s All The Technical Data You Could Ever Want On The Pebble Smartwatch
    | 5 For Business
  author_email: ''
  author_url: http://5forbiz.dev-shell.us/innovation/heres-all-the-technical-data-you-could-ever-want-on-the-pebble-smartwatch/
  date: '2013-02-11 06:47:39 -0500'
  date_gmt: '2013-02-11 14:47:39 -0500'
  content: '[...] on over to &#8220;VMs Are Free, Right?&#8221; to see a breakdown
    on a number of technical details for the Pebble [...]'
- id: 120
  author: Tips4Admin.Com | How to Send vCenter Alarm Notifications to SMS &amp; Other
    Online Services Using IFTTT
  author_email: ''
  author_url: http://www.tips4admin.com/virtualization/how-to-send-vcenter-alarm-notifications-to-sms-other-online-services-using-ifttt/
  date: '2013-02-22 20:57:39 -0500'
  date_gmt: '2013-02-23 04:57:39 -0500'
  content: '[...] since I could not go back to sleep after randomly waking up at 4am
    in morning. I was reading an article from Matt Cowger about something called a Pebble which
    I had not heard of before and it peaked [...]'
---
<p>This is an ongoing blog post about things I've learned about the Pebble Watch by pulling apart the firmware.</p>
<p>EDIT:  Much of this information has been merged into the wiki here:</p>
<p>http://wiki.skumler.net/wiki/Pebble</p>
<ol>
<li>The firmware is a simple zip file, with a JSON manifest, a resources file and the firmware file</li>
<li>The current firmware seems to have been codnamed 'tintin'</li>
<li>Looking at the strings in the firmware file, it appears that the third party bluetooth stack they use is the stonestreet '<a href="http://www.stonestreetone.com/bluetopia.cfm">BlueTopia</a>' stack</li>
<li><a href="https://www.google.com/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=1&amp;cad=rja&amp;ved=0CDEQFjAA&amp;url=http%3A%2F%2Fwww.st.com%2Fstm32f2&amp;ei=8ZIMUdqQBOb5ygHNuIG4Bg&amp;usg=AFQjCNHthsTDwxUr985irLJpCBkHYZKGJA&amp;sig2=modR6N_pHS68LkC2SY6kgQ&amp;bvm=bv.41867550,d.aWM">STM STM32 F2 Cortex M3</a> (we already knew it was Cortex M3, but not the specific model, I don't think).</li>
<li>The running environment appears to be <a href="http://www.freertos.org/">FreeRTOS</a>.</li>
<li>It looks like there are specific handlers for various message types.  At a minimum are: email, facebook, SMS, and 'pebble beach'.</li>
<li>It appears there is a maximum of 4 alarms you can set.</li>
<li>There are some funny debug log comments: "Seconds until alarm is crazy, got %d seconds" and "No one cleaned up after themselves before?", "Message is a command. Pebble don't take commands from nobody.", "WTF: This should never happen",</li>
<li>Alarm snooze options appear to be:10 minutes<br />
20 minutes<br />
45 minutes<br />
1 hour<br />
1 hour, 15 minutes<br />
1 hour, 30 minutes<br />
2 hours</li>
<li>It has an airplane mode thats not visible in settings (at least that I can find): "Airplane Mode" <strong> EDIT: Its been found if you toggle bluetooth mode.</strong></li>
<li>There appears to be Mfg test code left in:Mfg Func Test Battery<br />
Mfg Func Test Black<br />
Mfg Func Test Buttons<br />
Mfg Func Test Version</li>
<li>There are LOTS of assertions - thats a good thing.</li>
<li>Some cool ASCII art:^<br />
/ //o__o<br />
/ / __/<br />
 ______ /<br />
 /<br />
 ---- </li>
<li>Looks like there may be some sort of App-&gt;Watch authentication: Authentication Version: %d.%"</li>
<li>The code appears to be built with Eclipse</li>
<li>Its built in UTF-8, which means future support for RTL and other languages.</li>
<li>There are hints that it might support OBEX - which makes sense.</li>
<li>There are references to text rendering commands, which should make life easier for watchface makers.</li>
</ol>
<p>I haven't been able to figure out the format of the actual firmware BIN file yet, but its probably ARM specific, due to the Cortex.  Will have to do some FreeRTOS and ARM research.  Its 432KB</p>
<p>Same for the system resources file.  Haven't been able to pin down the format, but its 144KB.</p>
