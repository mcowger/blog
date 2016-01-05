---
layout: post
status: publish
published: true
title: 'Quick Post: VNX Snapshots Performance'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 451
wordpress_url: http://www.exaforge.com/?p=451
date: '2013-12-31 19:26:58 -0500'
date_gmt: '2013-12-31 19:26:58 -0500'
categories:
- VMware
tags: []
comments:
- id: 2269
  author: dynamox (@dynamoxxx)
  author_email: linuxrox@gmail.com
  author_url: http://storagemonkey.wordpress.com
  date: '2013-12-31 19:45:30 -0500'
  date_gmt: '2013-12-31 19:45:30 -0500'
  content: Customer is using thin or thick devices ?  I believe when you use VNX snapshots,
    thick devices get converted to thin devices, i have seen extremely long deletion
    of VNX snapshots for last set of snaps ..where LUN get converted back to thick.
- id: 2270
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-12-31 19:48:54 -0500'
  date_gmt: '2013-12-31 19:48:54 -0500'
  content: |-
    The customer is on all thin devices, so the conversion you mention isn't so much of an issue.  But is a good point, and something I should test now that you mentioned it.

    Additionally, when using this in the sort of 'pseudo' CDP method, there will always be some number of snaps on the device, as they are created hourly and set expire after 'X' days (probably 2 or 3 in this case).  So the scenario where the last snap disappears probably won't happen.
---
<p>I've recently been working on a design for VNX behind VPLEX, and wanted to do some pseudo-continuous data protection.  Now, normally one would use RecoverPoint on VPLEX for that, but there are some current limitations that made that not work for me - specifically, the fact that RecoverPoint CDP can only be used with one side of a VPLEX MetroCluster at a time.  If that side of the cluster goes down your data stays up (good), but you lose your CDP with it (bad).</p>
<p><a href="{{ site.baseurl }}/images/2013/12/Unknown-1.jpeg"><img class="alignright size-thumbnail wp-image-453" alt="Unknown-1" src="{{ site.baseurl }}/images/2013/12/Unknown-1-150x150.jpeg" width="150" height="150" /></a>So, the next option would have been to do RecoverPoint directly on the arrays (VNX) using the built in splitter...this is also a reasonable idea, but has a downside - RecoverPoint requires LUNs (called copy LUNs) that are the same size as the production devices.  So for a single CDP copy at either side of a 100GB production device, we are storing 300GB of data (+ another 50 or so for journals).  Because space is at a premium in this environment, I wanted to look at something a bit more 'thin.'</p>
<p><a href="{{ site.baseurl }}/images/2013/12/Unknown.jpeg"><img class="alignleft size-thumbnail wp-image-452" alt="Unknown" src="{{ site.baseurl }}/images/2013/12/Unknown-150x150.jpeg" width="150" height="150" /></a>So, I turned to the Pool-based snapshots in VNX that were released with the Inyo codebase (and still available of course in Rockies on the VNX 2 series).  I like these because they consume space from the same pool the production VM is in (no need to strand space in dedicated snap devices), and consume only as much space as was written to the LUN.  Lastly, they use a Redirect-on-Write technique to avoid the performance hit of Copy On First Write like the older SnapView snapshots did.  Sometimes these are called 'AdvancedSnapshots', as an FYI.</p>
<p>But - how impactful are the snapshots?  How do they impact performance?  I decided to test this, to see if would be a reasonable thing to propose to my customer.</p>
<p><img class="alignright size-medium wp-image-454" alt="Screenshot 2013-12-31 11.23.39" src="{{ site.baseurl }}/images/2013/12/Screenshot-2013-12-31-11.23.39-300x119.png" width="300" height="119" /></p>
<p>I set up a quick test.  I used a VNX5500 in my lab (so not the current VNX2 series, and also a much lower end model than the customer's VNX7600), and used 25x300GB 10K SAS drives along with 4x100GB EFDs in a RAID5 pool.  Carved out a 2TB LUN and allocated it to a host.</p>
<p>I started off with just a streaming test of 100% write traffic, and as able to achieve <strong>about 550MB/sec sustained</strong> (about 5.5GBit).  Next, I wrote a loop to write data, and create snapshots as I went:</p>
<pre>
[vmax] /mnt/A: for i in {1..96}; do ] 10:30 AM %
for&gt; naviseccli snap -create -res 4 -keepFor 1d 
for&gt; sudo dd if=/dev/zero of=/mnt/A/10G bs=1M count=10240
for&gt; echo $i
for&gt; done</pre>
<p>Every iteration of the loop therefore had 10GB of new data, as far as the array was concerned. I let this run 96 times, to simulate hourly snapshots over 4 days.  During this process, I kept track of the write performance.  Here's a snippet:</p>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 18.988 s, 565 MB/s
6</pre>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 17.2495 s, 622 MB/s
7</pre>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 17.4732 s, 615 MB/s
8</pre>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 19.1517 s, 561 MB/s
9</pre>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 18.7212 s, 574 MB/s
10</pre>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 18.8609 s, 569 MB/s
11</pre>
<p>As you can see, its very consistent, and there's no real degradation in performance while taking the snapshots.</p>
<p>Lastly, I ran a similar test while deleting all those snapshots in the background, to make sure that the customer wouldn't experience any degradation as the snapshots aged out and were deleted as time rolled on.  Another snippet:</p>
<pre>10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 19.1024 s, 562 MB/s
13
10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 20.2217 s, 531 MB/s
14
10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 21.1915 s, 507 MB/s
15
10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 20.8978 s, 514 MB/s
16
10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 19.8404 s, 541 MB/s
17
10240+0 records in
10240+0 records out
10737418240 bytes (11 GB) copied, 19.5746 s, 549 MB/s
18</pre>
<p>Again, not notable performance difference.  Thats some good news, as it means I can suggest this idea to a customer without concern.</p>
