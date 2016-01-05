---
layout: post
status: publish
published: true
title: Using SSH in ESXi (Password-Less)
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 15
wordpress_url: http://www.exaforge.com/?p=15
date: '2011-04-13 02:46:58 -0400'
date_gmt: '2011-04-13 02:46:58 -0400'
categories:
- VMware
- ESXi
tags:
- ESXi
- passwordless
- SSH
- VMware
comments:
- id: 3
  author: lamw
  author_email: william2003@gmail.com
  author_url: http://www.virtuallyghetto.com/
  date: '2011-04-13 03:11:00 -0400'
  date_gmt: '2011-04-13 03:11:00 -0400'
  content: "Matt,\n\nThough \"ssh\" symlink does not exist by default, the SSH client
    is available as \"dbclient\". Also note that this does not persist through a reboot,
    so adding this into something like /etc/rc.local will help keep the change around
    the next time system boots up\n\n~ # dbclient\nDropbear client v0.52\nUsage: dbclient
    [options] [user@]host[/port][,[user@]host/port],...] [command]\nOptions are:\n-p
    \n-l \n-t    Allocate a pty\n-T    Don't allocate a pty\n-N    Don't run a remote
    command\n-f    Run in background after auth\n-y    Always accept remote host key
    if unknown\n-s    Request a subsystem (use for sftp)\n-i    (multiple allowed)\n-L
    \ Local port forwarding\n-g    Allow remote hosts to connect to forwarded ports\n-R
    \ Remote port forwarding\n-W  (default 131072, larger may be faster, max 1MB)\n-K
    \  (0 is never, default 0)\n-I   (0 is never, default 0)\n-B  Netcat-alike forwarding\n-J
    \ Use program pipe rather than TCP connection\n\nWelcome to blogosphere\n\n--William"
- id: 4
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-04-13 03:14:00 -0400'
  date_gmt: '2011-04-13 03:14:00 -0400'
  content: |-
    Interesting.  Any idea why its called dbclient?  Edit: Duh - drop bear client.

    Thanks for the addition.
- id: 5
  author: John
  author_email: john@themeyers.us
  author_url: ''
  date: '2011-04-15 02:26:00 -0400'
  date_gmt: '2011-04-15 02:26:00 -0400'
  content: I believe you could also set the sticky bit on the symlink to force persistence.
- id: 6
  author: mcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-04-15 11:38:23 -0400'
  date_gmt: '2011-04-15 18:38:23 -0400'
  content: There's a bug in the current version that makes that not work :(
- id: 7
  author: sandy
  author_email: sandeeptt@yahoo.com
  author_url: ''
  date: '2012-06-08 04:41:49 -0400'
  date_gmt: '2012-06-08 12:41:49 -0400'
  content: how do you ssh passwordless to ESXi from Linux? I do not see any .ssh/authorized_keys
    on ESXi..
- id: 8
  author: Using SSH in ESXi (Password-Less) | Felipe Alvarez&#039;s blog
  author_email: ''
  author_url: http://felipe1982.com/blog/2012/11/08/using-ssh-in-esxi-password-less/
  date: '2012-11-07 20:57:26 -0500'
  date_gmt: '2012-11-08 04:57:26 -0500'
  content: '[...] to exaforge we can now setup Public Key Authentication and
    Use SSH to connect from ESXi to another (Linux) [...]'
- id: 2928
  author: septic tank
  author_email: sheilaemmett@googlemail.com
  author_url: http://septictankasahi.com
  date: '2014-03-12 01:22:59 -0400'
  date_gmt: '2014-03-12 01:22:59 -0400'
  content: "I do accept as true with all of the concepts you \r\nhave presented for
    your post. They're very convincing and can \r\ncertainly work. Nonetheless, the
    posts are too brief \r\nfor newbies. Could you please lengthen them a little from
    next time?\r\nThanks for the post."
---
<p>Looking into this, its a little funny - you can SSH into ESXi, but not out.  Dropbear has the ability to create a valid keypair, but theres no actual ssh binary in ESXi.  However, you can make it happen.</p>
<p>1) "Create" an ssh symlink to dropbear:</p>
<pre style="padding-left:30px;">ln -s /sbin/dropbearmulti /bin/ssh</pre>
<p>This works because dropbearmulti is a <a href="http://www.redbooks.ibm.com/abstracts/tips0092.html?Open">multicall</a> binary, which allows it to change behavior depending on how you execute it.</p>
<p>2) Create a keypair:</p>
<pre style="padding-left:30px;">dropbearkey -t dss -f privatekeyfile -s 1024</pre>
<p>Why 1024?  Because thats the only keylength supported by DSS keys!.  You'll end up with a file in the current directory called 'privatekeyfile' and the system will output a public key in SSH format on the screen:</p>
<pre style="padding-left:30px;">~ # dropbearkey -t dss -f private -s 1024

Will output 1024 bit dss secret key to 'private'

Generating key, this may take a while...

Public key portion is:

ssh-dss AAAAB3NzaC1kc3MAAACBAJbXscSKNxkxs3NYfMgMLs8tsh3iio9vFN3fzq8/5HrsgcGK3gHc+SQlLmhtP...hostname.domain</pre>
<p>Copy all the lines of this starting from "ssh-dss" through to the end of "hostname.domain" to your clipboard.</p>
<p>3) Add this copied public key to your Linux host in the right location - usually ~/.ssh/authorized_keys:</p>
<pre style="padding-left:30px;">linuxhost% cat .ssh/authorized_keys
ssh-dss AAAAB3NzaC1kc3MAAACBANPYWCXvqAVK95Xa0qM1rUPM7h2CWB85d2Qk3paYsRU6x....</pre>
<p>4) Now use the private key to make sure that it works from ESXi:</p>
<pre style="padding-left:30px;">~ # ssh -i privatekeyfile username@domain.lan

Last login: Tue Apr 12 15:01:15 2011 from domain.lan
[user@host] (Linux 2.6.18-194.26.1.el5)%</pre>
<p>Life is good!</p>
