---
layout: post
status: publish
published: true
title: Installing the vCops Symmetrix Collector Plugin
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 212
wordpress_url: http://www.exaforge.com/?p=212
date: '2011-12-22 10:33:00 -0500'
date_gmt: '2011-12-22 18:33:00 -0500'
categories:
- VMware
tags:
- Symmetric
- Symmetrix
- vCenter Operations
- vCops
- VMAX
- VMware
comments: []
---
<p>My colleague, <a href="http://twitter.com/#!/vtexan">Tommy Trogden</a>, recently asked me if I could demonstrate the native Symmetrix plugin for vCenter operations.  This is my favorite kind of request, because it means I get to try something new :).</p>
<p>One thing I like about how this collector was designed is that it uses the existing TTP (Text Transform) performance files that the Symmetrix Solutions Enabler application already outputs, making it easy for existing Symmetrix admins.  As a result, I won&rsquo;t go into installing SMC/Solutions Enabler, because most shops will have that setup.  If anyone wants, in the future I could do a post on it.</p>
<p>Without further ado &ndash; the installation process for the Symmetrix adapter for vCenter Operations.</p>
<p>1) Download the plugin itself from your VMware representative (its not currently available on the website).  Also download the installation tech note at the same time.</p>
<p>2) Because my vCops server runs on Windows, and my SE system is Linux, I need a way to access the Linux filesystem from Windows.  I chose to use SMB (Samba) to do this, simply because its easy to setup.</p>
<p>3) First, we need to configure an Samba export on the Linux side.  Specifically, we need to export the directory with the TTP files, which are in (by default) /var/symapi/stp.  So lets share that out.  First, lets make sure the Samba service is running:</p>
<pre>[root@smc samba]# /etc/init.d/smb start

Starting SMB services:                                     [  OK  ]

Starting NMB services:                                     [  OK  ]</pre>
<p>Then lets configure it by adding the following to /etc/samba/smb.conf</p>
<pre>[global]

    security = share

    ;passdb backend = tdbsam

    guest account = root

    ;yes, this is horrible, but this could be fixed in a production environment with some clever umask settings on the linux side

[symapi]

    comment = TTP Files

    path = /var/symapi/stp/

    public = yes

    writable = yes

    printable = no

    guest ok = yes

    browseable = yes
</pre>
<p>We then restart SMB:</p>
<pre>[root@smc samba]# /etc/init.d/smb restart

Shutting down SMB services:                                [  OK  ]

Shutting down NMB services:                                [  OK  ]

Starting SMB services:                                     [  OK  ]

Starting NMB services:                                     [  OK  ]</pre>
<p>4) Now we need to configure the STP daemon in solutions enabler to capture what we need/want.  Specifically, the following options in the in /var/symapi/config/daemon_options need to be changed:</p>
<pre>storstpd:dmn_run_ttp = enable

storstpd:ttp_collection_interval = 1
storstpd:ttp_rdflnk_metrics = enable
storstpd:ttp_se_tcp_metrics = enable
storstpd:ttp_se_nw_metrics = enable

storstpd:ttp_dev_metrics = disable
storstpd:ttp_dgdev_metrics = enable
storstpd:ttp_se_tcp_metrics = enable
storstpd:ttp_se_nw_metrics = enable
storstpd:ttp_se_nwi_metrics = enable
storstpd:ttp_re_sg_metrics = enable
storstpd:ttp_re_nwc_metrics = enable
storstpd:ttp_rdflnk_metrics = enable
storstpd:ttp_se_tcp_metrics = enable
storstpd:ttp_se_nw_metrics = enable</pre>
<p>Save the file, then we restart storstpd to make it reread the config:</p>
<pre>[root@smc config]# stordaemon start storstpd

  Waiting for daemon to start.  This may take several seconds.

[root@smc config]# stordaemon list

Available Daemons  ('[*]': Currently Running):

[*] storapid              EMC Solutions Enabler Base Daemon

    storgnsd              EMC Solutions Enabler GNS Daemon

    storrdfd              EMC Solutions Enabler RDF Daemon

[*] storevntd             EMC Solutions Enabler Event Daemon

[*] storwatchd            EMC Solutions Enabler Watchdog Daemon

    storsrmd              EMC Solutions Enabler SRM Daemon

[*] storstpd              EMC Solutions Enabler STP Daemon

[*] storsrvd              EMC Solutions Enabler SYMAPI Server Daemon</pre>
<p>Once its running, we can check that sep directory from before and we see some new contents:</p>
<pre>[root@smc ttp]# ls -la /var/symapi/stp/

total 16

drwxr-xr-x  4 root root 4096 Dec 21 14:55 .

drwxr-xr-x 14 root root 4096 Jun  8  2011 ..

drwxr-xr-x  3 root root 4096 Jan  5  2011 spa

drwxr-xr-x  3 root root 4096 Dec 21 14:55 ttp</pre>
<p><strong><br />
 </strong><br />
 <strong><br />
 </strong><br />
 We have a new ftp directory - win!</p>
<p>Now, the last part is we need to force the STP daemon to archive the files off every 5 minutes, by adding the following cron job:</p>
<pre>[root@smc 000194900728]# crontab -l

2-57/5 * * * * /opt/emc/SYMCLI/bin/stordaemon action storstpd -cmd archive  &gt; /dev/null</pre>
<p>5) Now, install the adapter into your vCops server:</p>
<p>Run the downloaded executable:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324511653.jpg" alt="myWPEdit Image" /></p>
<p>And follow all the defaults.</p>
<p>6) Now, restart the vCops service using standard methods.</p>
<p>7) Now, lets setup the collector.</p>
<p>Go into the adapter instances part of vCops:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324511928.jpg" alt="myWPEdit Image" /></p>
<p>Then select the adapter type and hit the '+' button:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324511952.jpg" alt="myWPEdit Image" /></p>
<p>Fill in the required information:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324511975.jpg" alt="myWPEdit Image" /></p>
<p>And test to make sure it works:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324511990.jpg" alt="myWPEdit Image" /></p>
<p>NOTE: You CANNOT use a mapped driver letter, because services cannot access them in Window (pshaw!  Use Linux!)</p>
<p>8) Now, we wait for the files to get processed.  Give it 15 minutes or so to really gather some reasonable data and import it.</p>
<p>9)  Take a look at the environment detail, and you should see your arrays:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324516531.jpg" alt="myWPEdit Image" /></p>
<p>And the detail for it:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324516556.jpg" alt="myWPEdit Image" /></p>
<p>Seems like there is plenty of data being collected, including throughput of the FEs, BEs, individual ports, cache stats and the RDF link performance.  Cool!  On my (pretty small) Symmetrix VMAX-SE, I have almost 1000 metrics being collected.</p>
<p>As for what the dashboards from this data can look like, I've created a few samples.  First, its important to monitor the health of the cache in a Symmetrix - specifically to monitor the number of write pending tracks compared to the max write pending limit.  Here's a dashboard showing that.  Clearly, this is pretty healthy.</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324578695.jpg" alt="myWPEdit Image" /></p>
<p>Next, we look at the total IOPs and Read / Write throughput on the array, just to get a sense of how hard its working overall:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324578740.jpg" alt="myWPEdit Image" /></p>
<p>Last, we look at the FA ports supporting my VMware environment, to make sure they aren't getting killed:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1324578771.jpg" alt="myWPEdit Image" /></p>
<p><img src="http://www.exaforge.com/images/2011/12/screen-shot-2011-12-28-at-4-17-41-pm.png" alt="" width="653" height="625" /></p>
