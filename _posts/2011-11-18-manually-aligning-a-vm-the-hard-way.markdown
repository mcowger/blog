---
layout: post
status: publish
published: true
title: Manually Aligning a VM the Hard Way
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 157
wordpress_url: http://www.exaforge.com/?p=157
date: '2011-11-18 17:39:40 -0500'
date_gmt: '2011-11-19 01:39:40 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 51
  author: Nick Howell
  author_email: ''
  author_url: http://twitter.com/that1guynick
  date: '2011-11-30 21:40:00 -0500'
  date_gmt: '2011-12-01 05:40:00 -0500'
  content: |-
    Matt,

    First of all, you're right.  We're all human, and in the interest of helping rather than bashing, I've kept from posting what I'd written already, and am glad we could help some fellow storage brethren fix an issue.  At the end of the day, ESPECIALLY in the HoL, it's all about the customers and their experience.  It's unfortunate that it had to be discovered during/after rather than before.

    That said, I'm going to simply update that post you referenced with a cross-link back to here for reference since you've written it up for us!

    Thanks for the candor, and thanks EMC for being big about it and owning up to it.  Respectable, indeed.

    As far as your interest in "how" we discovered it, I will tell you that some of those VNX VSA's were deployed/provisioned on our arrays during the normal operations in the HoL, and some "misaligned I/O's" were discovered by one of our engineers.  We started digging and voila.  We have some screenshots and datapoints, but judging by what I'm seeing in this post, nothing would be news to you now.  :)

    -Nick
- id: 52
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-11-30 21:43:00 -0500'
  date_gmt: '2011-12-01 05:43:00 -0500'
  content: Fair enough - I bet readers would love to see those screen shots though!
- id: 53
  author: Tired_of_Childish_Vendors
  author_email: not@important.com
  author_url: ''
  date: '2011-12-01 04:48:00 -0500'
  date_gmt: '2011-12-01 12:48:00 -0500'
  content: 'This is like watching Congress work....if either side wanted candor they
    would not have shared these findings and solutions on a public blog....grow up
    boys! Some of us are actually trying to run IT shops not run for office!! '
- id: 54
  author: Matt Cowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2011-12-01 10:05:00 -0500'
  date_gmt: '2011-12-01 18:05:00 -0500'
  content: |-
    I'm curious how sharing this information publicly (as opposed to hiding it) is a lack of candor?


    The point of this point is to show you, someone running an IT shop, how to fix an unaligned VM with a complex filesystem layout.
- id: 55
  author: ''
  author_email: nick@nicholasweaver.com
  author_url: ''
  date: '2011-12-01 14:20:00 -0500'
  date_gmt: '2011-12-01 22:20:00 -0500'
  content: Nicely done Matt, I just learned a couple new tricks!
- id: 56
  author: ''
  author_email: nick@nicholasweaver.com
  author_url: ''
  date: '2011-12-01 14:21:00 -0500'
  date_gmt: '2011-12-01 22:21:00 -0500'
  content: Good thing the VSA is non-production right?  Heh...
- id: 57
  author: Nick Howell
  author_email: ''
  author_url: http://twitter.com/that1guynick
  date: '2011-12-01 15:13:00 -0500'
  date_gmt: '2011-12-01 23:13:00 -0500'
  content: |+
    @b82551bdccaf9fb06104b4b3f8f4f723:disqus ,

    Here's the non-Congress no-BS version:

    The VMware Hands-on-Lab is a multi-vendor effort.  There was storage gear used from both EMC and NetApp, among others.

    All of the VMware labs were using virtual storage appliance versions of the VNX, and those VSA's were deployed as VM's across all of those storage vendors on the backend, along with the rest of the VM's needed for each lab.  With the exception of one.  The NetApp lab used our VSIM DataONTAP simulator.  We made an extra effort beforehand to ensure that it was aligned in the Catalog responsible for deploying each lab "kit" to the HoL students.

    A NetApp engineer was working on something else on some of our storage while the HoL was in full swing and noticed misaligned I/O coming into the storage.  Upon further investigation, and taking one of the labs himself, he was able to figure out the misaligned IO was coming from the VNX appliance.  We're certain this had a bit of a compounding effect, but was likely offset (pun intended) by the sheer amount of gear that was available in the HoL.

    I made a reference comment about it in a recent VMworld wrap-up post, calling it out, and Matt addressed it here in this post, and how to fix it going forward.

    -Nick

- id: 58
  author: Peter Learmonth
  author_email: ''
  author_url: http://twitter.com/titaniumlegs
  date: '2011-12-02 19:18:00 -0500'
  date_gmt: '2011-12-03 03:18:00 -0500'
  content: |-
    Peter from NetApp here.  I'm with you on this not being a political thread.  It's pretty tame compared to some of the stuff that goes on between other people from both companies!  (Let's keep it that way!  ;-)

    Anyway, I'm the geek - I mean - "engineer" Nick is talking about.  One of the things we had expected, because we've seen it in pretty much every lab in the past, was a certain amount of I/O misalignment.  So I was checking some of our new alignment metrics, including the "worst offenders list", and voila, most of the misaligned I/O was from VMs with "VNX" in their names.  That's not a jab or anything.  The prominence is because, as you guys have said, the VNX was the virtual storage for most of the labs.

    My blog on the newish alignment commands in ONTAP.
    https://communities.netapp.com/community/netapp-blogs/getvirtical/blog/2011/05/13/new-vmdk-misalignment-detection-tools-in-data-ontap-735-and-802

    And, yes, I did the EMC lab partly to learn, and partly to take a look at fdisk -lu in the VNX sim.

    Final note:  Although not officially supported, mbralign does well with LVM.  I'm guessing it would be able to fix alignment on the VNX sim, but you would still have to fix GRUB.

    Share and enjoy!
- id: 59
  author: Martin K
  author_email: martin.koelbl@univie.ac.at
  author_url: ''
  date: '2012-02-23 12:22:00 -0500'
  date_gmt: '2012-02-23 20:22:00 -0500'
  content: I have downloaded the VM image of NetApp's Data ONTAP Simulator and it's
    not aligned! (Version 8.1 RC2)
---
<p>Some of you may know that the EMC VNX VSA was the virtualized storage used on most of the VMware Hands On Labs.  Its was a great chance to really help out our VMware brethren.</p>
<p>However, there was a problem, one that we didn't catch.  Vendors are made up of humans, and none of us is perfect, including EMC.  Our friends over at NetApp noted (via <a href="http://datacenterdude.com/2011/10/vmworld-2011-hands-on-labs-the-results-the-stats-the-fud/">Nick Howell's blog</a>) that they had discovered that the VNX VSA was not a properly aligned VM (e.g. it wasn't aligned on a sector boundary).  Nick mentioned that he was going to clarify how they determined it (Nick?  Maybe you can write that post? Hint! Hint!), but in the meantime, I'd like to thank them (NetApp) for helping us out and letting us know.  While we may be competitors, when it comes to the VMworld HoL, we can (and do!) put aside our differences and just help out.</p>
<p>So I went about fixing that oversight, because VM alignment is important and should be fixed, especially when running hundreds of them like at the Labs.  I would have used <a href="http://www.twitter.com/lynxbat">Nick Weaver</a>'s awesome<a href="http://nickapedia.com/2011/11/03/straighten-up-with-a-new-uber-tool-presenting-uberalign/"> UBERAlign</a> tool, but it doesn't support LVM volumes quite yet, which the VSA uses.  So I had to go after it manually.   I also couldn't do live (for obvious reasons - you can't realign while powered on).  Here was the process I undertook:</p>
<p><strong>Examine the File System - </strong>The first order of business was to look at the configuration.  The easiest way to do this was to attach the VM's disk to an existing Linux VM as a secondary disk:</p>
<ol>
<li><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-38-51-pm.png"><img class="aligncenter size-full wp-image-161" title="Screen Shot 2011-11-18 at 4.38.51 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-38-51-pm.png" alt="" width="647" height="501" /></a><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-39-04-pm.png"><img class="aligncenter size-full wp-image-162" title="Screen Shot 2011-11-18 at 4.39.04 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-39-04-pm.png" alt="" width="638" height="502" /></a><br />
This way, I can make changes to the disk without having to do it live.  In this case, I noted that the single disk on the VM was broken into 2 partitions using the 'fdisk' command:<img class="aligncenter size-full wp-image-163" title="Screen Shot 2011-11-18 at 4.44.48 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-44-48-pm.png" alt="" width="721" height="197" /></p>
<p>There was a single partition marked as bootable (/dev/sdc1 in this case) and a second marked as lvm (/dev/sdc2).   Note that neither starts on a 512-byte sector boundary - they are unaligned.  This is a pretty common Linux structure, and usually means that the boot partition contains basically grub and the kernel (usually ext2 or ext3), and the second partition contains one or more LVM logical volumes.  As a first step, I mounted /dev/sdc1 (the boot partition) at /tmp/boot so that I could back up its contents using the mount command:</p>
<blockquote style="margin:0 0 0 40px;border:none;padding:0;">
<pre>mount /dev/sdc1 /tmp/boot</pre>
</blockquote>
<p>Then I investigated the LVM volume.  I ran '<em>pvs</em>', which looks for LVM Physical Volumes (its short for <em>pvscan</em>).  I saw a single Physical Volume (as expected):</p>
<p><img class="aligncenter size-full wp-image-164" title="Screen Shot 2011-11-18 at 4.45.04 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-45-04-pm.png" alt="" width="717" height="86" />Then, I ran vgscan to search for volume groups. Because this is a pretty simple VM, there is only one volume group (VolGroup00):</p>
<p><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-45-21-pm.png"><img class="aligncenter size-full wp-image-165" title="Screen Shot 2011-11-18 at 4.45.21 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-45-21-pm.png" alt="" width="699" height="77" /></a></p>
<p>Further, ontop of that volume group, there are 2 Logical Volume on top of that Logical Volume:</p>
<p><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-45-34-pm.png"><img class="aligncenter size-full wp-image-166" title="Screen Shot 2011-11-18 at 4.45.34 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-45-34-pm.png" alt="" width="695" height="90" /></a></p>
<p>Lets figure out what those are (just try mounting them):<a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-47-29-pm.png"><img class="aligncenter size-full wp-image-167" title="Screen Shot 2011-11-18 at 4.47.29 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-47-29-pm.png" alt="" width="694" height="46" /></a></p>
<p><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-50-17-pm.png"><img class="aligncenter size-full wp-image-169" title="Screen Shot 2011-11-18 at 4.50.17 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-4-50-17-pm.png" alt="" width="686" height="98" /></a>OK, now we know that one of them is the root filesystem (ext3) and the other is the swapspace. Pretty Cool!</li>
<li><strong>Copy Off Data - </strong>While it can be possible to do this in-situ (UBERAlign can do this!), I find it easier and clearer to do this against a new disk.  By doing it this way, we also reduce the fragmentation in the filesystem and increase the free/zero space, making the VM as small as it can be for best performance.  So, now that we have both the boot and root filesystems mounted, we are just going to copy off the data.  We are going to use rsync for this, because it gives nice status updates, is fast and supports sparse files efficiently (which are used in the VSA).  We copy the data like this:
<pre>rsync -avS /tmp/boot/* /backup/boot/
rsync -avS /tmp/lv0/* /backup/root/</pre>
<p>In my case, this took about 10 minutes, but it depends on how fast your storage is, and how much data you have.</li>
<li><strong>Recreate Partition Table - </strong>We now need to recreate the partition table in such a fashion that its aligned.  In short, we need to make sure that partitions start on a 512 byte boundary, which fdisk doesn't do by default (ugh!).  So lets use GNU parted to modify this partition table:You'll get a number of errors about informing the kernel, but this is normal.  At this point, its best to reboot your helper VM, just so we can clear out any of the LVM state (its possible to do this live, but its a pain).<a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-04-16-pm.png"><img class="aligncenter size-full wp-image-171" title="Screen Shot 2011-11-18 at 5.04.16 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-04-16-pm.png" alt="" width="693" height="299" /></a><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-06-33-pm.png"><img class="aligncenter size-full wp-image-172" title="Screen Shot 2011-11-18 at 5.06.33 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-06-33-pm.png" alt="" width="693" height="140" /></a>How did I choose the starting values of 4 and 120?  Well, I chose them because I used parted's ability to set the units:units MiB
<p>This ensures that all future numerical values are in Mibibytes, and will therefore be aligned on a 512 byte boundary.  It also makes it easier to think in Mibibytes.  <strong>This is the most important step, so get this right!</strong></li>
<li><strong>Create LVM Setup and Filesystems</strong>- Now we need to recreate the setup we had before.  First, we create the physical volume, volume group, and logical volumes as they existed before:<a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-20-26-pm.png"><img class="aligncenter size-full wp-image-173" title="Screen Shot 2011-11-18 at 5.20.26 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-20-26-pm.png" alt="" width="693" height="123" /></a><a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-22-29-pm.png"><img class="aligncenter size-full wp-image-174" title="Screen Shot 2011-11-18 at 5.22.29 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-22-29-pm.png" alt="" width="688" height="93" /></a>The '-l 100%FREE' technique tells LVM to create the Logical Volume with whatever space remains in the volume group.  In other words 'Use the rest'.<br />
<img class="aligncenter size-full wp-image-176" title="Screen Shot 2011-11-18 at 5.23.53 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-23-53-pm.png" alt="" width="589" height="327" /><img class="aligncenter size-full wp-image-177" title="Screen Shot 2011-11-18 at 5.24.03 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-24-03-pm.png" alt="" width="664" height="336" /><img class="aligncenter size-full wp-image-178" title="Screen Shot 2011-11-18 at 5.24.24 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-24-24-pm.png" alt="" width="677" height="83" /></li>
<li><strong>Copy Data Back</strong> - Now we copy all the data back, using the same method as above (rsync):
<pre>rsync -avS  /backup/boot/* /tmp/boot/
rsync -avS  /backup/root/* /tmp/root/</pre>
</li>
<li><strong>Fix the Bootloader</strong>- Now that we have all the data copied, you'd think we were done, but we have one more step.  Because we moved everything around, the bootloader, called grub, will be confused about where its boot files are (because it uses disk offsets).  We need to reinstall the boot loader.  Fortunately, this is a simple process.  We start the grub bootloader utility, then tell it where to consider its grub root (NOT the same as the root filesystem).  After that we have it setup grub on the drive (in this case hd2, because its the third drive in this system):<a href="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-36-25-pm.png"><img class="aligncenter size-full wp-image-179" title="Screen Shot 2011-11-18 at 5.36.25 PM" src="http://www.exaforge.com/images/2011/11/screen-shot-2011-11-18-at-5-36-25-pm.png" alt="" width="705" height="208" /></a></li>
<li><strong>Disconnect - </strong>From here, just shutdown your helper VM, disconnect the VM's HD from the helper, and boot your old VM.  It should boot up just fine!</li>
</ol>
<div><span style="line-height:18px;">Phew!  That was a lot of work, and now you know how to do it!   Just use UBERAlign instead, if you can!</span></div>
