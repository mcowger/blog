---
layout: post
status: publish
published: true
title: VCDX Process / Experience
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 34
wordpress_url: http://www.exaforge.com/?p=34
date: '2011-04-12 18:35:11 -0400'
date_gmt: '2011-04-13 01:35:11 -0400'
categories:
- Uncategorized
- VMware
tags:
- VCDX
- VMware
comments:
- id: 14
  author: Christopher Kusek
  author_email: christopher.kusek@pkguild.com
  author_url: http://pkguild.com
  date: '2011-04-13 04:33:00 -0400'
  date_gmt: '2011-04-13 04:33:00 -0400'
  content: Congrats congrats congrats!!!!
- id: 15
  author: Jason Boche
  author_email: jason@boche.net
  author_url: ''
  date: '2011-04-13 15:45:00 -0400'
  date_gmt: '2011-04-13 15:45:00 -0400'
  content: You said yor aren't a consultant.. What is your title/role?
- id: 16
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-04-13 15:49:00 -0400'
  date_gmt: '2011-04-13 15:49:00 -0400'
  content: |-
    At the time I achieved the certification, I was a mere senior systems/storage engineer for Salesforce.com.  My job was to design and build big environments (and do some Ops work).  For me, that meant that a lot of the documentation steps that the VCDX application required weren't relevant to my job (Next Steps, Operational Documentation) as we didn't do things like that.

    Nowadays I'm a Solutions Architect for the 3PAR part of HP, so the consultancy aspect to my job has increased significantly.
- id: 17
  author: Jason Boche
  author_email: jason@boche.net
  author_url: ''
  date: '2011-04-13 16:57:00 -0400'
  date_gmt: '2011-04-13 16:57:00 -0400'
  content: |-
    Excellent - thanks Matt.

    Jas
- id: 1364
  author: VCDX Preparation index - VirtualizeTips | VirtualizeTips
  author_email: ''
  author_url: http://www.virtualizetips.com/2013/09/19/vcdx-preparation-index/
  date: '2013-09-19 18:58:30 -0400'
  date_gmt: '2013-09-19 18:58:30 -0400'
  content: '[&#8230;] Cowger &#8211; VCDX Process and Experience [&#8230;]'
---
<p>So many people have posted about their experience with the VCDX project, and many of those posts were a huge help to me, in the words of Jason Boche (a fellow VCDX), here’s my point of view to pay it forward and help those who come after me.</p>
<p>I wont spend a lot time on the process, as its been covered quite clearly.  Some references would be Jason Boche's blog (<a href="http://www.boche.net/blog/index.php/2010/02/14/my-vcdx-defense-experience/">http://www.boche.net/blog/index.php/2010/02/14/my-vcdx-defense-experience/</a>) ,Chris Kranz’s blog (<a href="http://www.wafl.co.uk/vcdx-journey/">http://www.wafl.co.uk/vcdx-journey/</a>) or Jason Nash's blog (<a href="http://jasonnash.wordpress.com/2010/09/03/my-vcdx-defense-or-how-i-flew-to-san-francisco-to-choke/">http://jasonnash.wordpress.com/2010/09/03/my-vcdx-defense-or-how-i-flew-to-san-francisco-to-choke/</a>)– they are all very accurate.</p>
<p>Instead, I’d just like to give my thoughts on specific parts in bullet format before I forget anything.</p>
<ul>
<li>VCP – This one should be a breeze for you.  If its not, I’d suggest considering putting some more time under your belt before attempting this process.</li>
<li>Enterprise &amp; Design Admin Exams.  These probably wont be a breeze, but they aren’t all that difficult if you spend all day doing VMware related stuff.  If you are a Microsoft-center person, get familiar with the command line methods of doing things, as there are plenty of questions around that, both in the Lab and multiple-choice sections.  The lab section does allow for use of the man pages and help, so don’t be afraid to look it up.</li>
<li>VCDX Application – This was the hardest part for me.  There’s really 2 parts to this, and they are fundamentally different.  In both, I’d urge you to think like a consultant.  I’m not a consultant and never have been, so this was key to getting my head around things.</li>
<li>Assume nothing – not even that your audience knows what ‘LUN’ stands for.</li>
<li>Document everything – even the things that are standard in your organization.  In my company, we have well known standards for LUN naming, datastore, naming etc, so my initial application materials simply didn’t cover that.  Same went for things we wouldn’t do internally like ‘Next Steps’ and ‘Timelines’.  What the application says about that (“If they aren’t included it’s a reject”) is totally true – the committee will reject it on that (mine did on my first 2 tries!).  If I could make a specific recommendation, see if your VMware PSE or TAM or SE or Salesrep can get you a blank copy of their PSO engagement template – it’s a very good guide for what you need to include as well as the level of detail expected.</li>
<li>Application – don’t overanalyze this.  Take your time and fill it in well.  Look at Duncan Epping’s blog entry about this, as well as John Arrasjid’s</li>
<li>Technical+
<ul>
<li>This is not just a technical exam – your communication and design and evaluation skills are being tested.  Most have mentioned to ‘know your design’ and this is definitely true.  Know why you chose 3 vSwitches over 2, and why you didn’t choose 4.  Know why you didn’t do Etherchannel, and why you did choose Dell over HP or Sun.  These are all more than technical decisions, and you need to be able to explain them</li>
</ul>
</li>
<li>Defense
<ul>
<li>Presentation
<ul>
<li>This will go approx 2x faster than you think, no matter how fast you think you can talk.  You will be interrupted for questions and clarifications, etc.  You will need all of the time allotted, and when that clock starts blinking red, you will be astonished at where the time went.  The best that I can offer here is to have a strong outline for your presentation that you can follow to avoid going on tangents that eat time.  That, and have lots of slides with diagrams.  I had 3 or 4, and wished I had 6 or 7.</li>
</ul>
</li>
<li>Design
<ul>
<li>This was the hardest part for me personally.  You will be given requirements to meet, but just like normal customers and clients, they may be incomplete or wrong ☺.  Be prepared to ask a lot of questions and assume nothing.  As others have said, the point is NOT to complete a design in the 30 minutes allotted (I doubt its even possible).  The point is to show that you are an Expert Designer by the kinds of questions you ask and how you think out  loud.</li>
<li>Think Out Loud.  Its my style to think silently for 5 minutes evaluating information before stating an idea, but that wont work in a defense.  Practice speaking your evaluations of information out loud, because the committee will need to hear it.</li>
</ul>
</li>
<li>Troubleshooting
<ul>
<li>See above about thinking out loud.  Getting the right answer isn’t the primary concern here (though I’m sure it helps).  Talk out loud about how to solve the problem you are facing.  Even if you haven’t the foggiest clue, talk out loud about what you’d be looking for.  Your thought process is whats important</li>
</ul>
</li>
</ul>
</li>
<li>DON’T PANIC
<ul>
<li>The committee isn’t out to get you.  I got the feeling (as did others) they wanted me to pass – I just needed to provide them the proof they could point to so they could pass me with clear conscience.</li>
</ul>
</li>
<li>Don’t waste breaks - Your email isn’t that important.  Your twitter isn’t that important.  Your voicemail isn’t important.  Walk around.  Look at the art.  Meditate.</li>
</ul>
<p>I might have more to add later, but for now chew on that!  I found out that morning that I passed and was granted VCDX #52, so maybe I'm not nuts with the above.</p>
<p>&nbsp;</p>
