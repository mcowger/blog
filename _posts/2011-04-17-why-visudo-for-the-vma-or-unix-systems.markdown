---
layout: post
status: publish
published: true
title: Why visudo for the vMA or Unix systems.
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 47
wordpress_url: http://www.exaforge.com/?p=47
date: '2011-04-17 09:36:30 -0400'
date_gmt: '2011-04-17 16:36:30 -0400'
categories:
- VMware
- ESXi
tags:
- linux
- sudo
- sudoers
- unix
- visudo
- vma
- VMware
comments: []
---
<p>On Twitter recently, <a href="http://twitter.com/maishsk/status/59621547995963392">Maish Saidel-Keesing posed an interesting question</a>: "Why bother with visudo at all?".  The implication/suggestion here is that visudo is nothing more than alias to 'vi /etc/sudoers'. Fortunately, its not.</p>
<p>visudo actually creates a copy of the sudoers file, edits that using vi (or whatever editor you have set for $VISUAL in your shell), <strong>checks the syntax </strong>and then, on success, copies it into place.</p>
<p>What if you didn't use visudo and just edited the file directly, but made a mistake?  Well, here's an example</p>
<pre>[vi-admin@vma ~]$ sudo -l
&gt;&gt;&gt; sudoers file: syntax error, line 101 &lt;&lt;&lt;
sudo: parse error in /etc/sudoers near line 101</pre>
<p>As you can see, once you have a syntax error, you dont get to use sudo at all.  You;ll have to login as root directly (might be hard if you've disabled the root account like in the vMA) or wait for someone with the root password to fix it for you.  However, if I make a mistake  using visudo, its very clear about telling me:</p>
<pre>[vi-admin@vma ~]$ sudo visudo</pre>
<pre>Password:
Warning: undeclared User_Alias `CRAP' referenced near line 101
&gt;&gt;&gt; sudoers file: syntax error, line 101 &lt;&lt;&lt;
What now?</pre>
<pre>Options are:
(e)dit sudoers file again
e(x)it without saving changes to sudoers file
(Q)uit and save changes to sudoers file (DANGER!)</pre>
<p>visudo very clearly saves me from a major mistake (well, at least warns me about it).</p>
<p>So what do you do if you want to edit the file for pushing out to a large number of machines and dont want to load it on the current system.  Well, visudo has you covered there too.  Use it in 'check' mode:</p>
<pre style="padding-left:30px;">[vi-admin@vma ~]$ visudo -c -f sudoers.testing
sudoers.testing file parsed OK</pre>
<p>Life is good!  Thanks to <a href="http://technodrone.blogspot.com/">Maish</a> for the idea.</p>
