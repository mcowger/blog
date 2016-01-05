---
layout: post
status: publish
published: true
title: Multipath Identification in ESX
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 87
wordpress_url: http://www.exaforge.com/?p=87
date: '2011-07-25 11:12:08 -0400'
date_gmt: '2011-07-25 18:12:08 -0400'
categories:
- VMware
- ESXi
tags: []
comments:
- id: 132
  author: www.insectkits.com
  author_email: FierstKrone33@aol.com
  author_url: http://www.insectkits.com/contents/en-us/d18.html
  date: '2013-05-23 01:40:00 -0400'
  date_gmt: '2013-05-23 01:40:00 -0400'
  content: Very great post. I simply stumbled upon your blog and wanted to say that
    I've really enjoyed surfing around your weblog posts. After all I will be subscribing
    in your rss feed and I'm hoping you write again soon!
---
<p>There was an interesting post on the <a href="http://communities.vmware.com/message/1797622#1797622">communities forum recently regarding multipathing</a> and how ESXi identifies a given device as being the 'same' as another for multipath purposes.</p>
<p><strong>Why Multipathing Drivers?</strong></p>
<p>Why do we need a multipathing driver to start with?  We need it because every HBA card see's the volumes it sees in isolation - they generally have no idea that another card or path exists.</p>
<p>Here's the output of esxcfg-mpath -l from a host with multipathing configured (many thanks to<a href="http://www.boche.net"> Jason Boche</a> for the example):</p>
<pre style="padding-left:30px;">iqn.1998-01.com.vmware:esxi2-35bac89b-00023d000002,iqn.1992-04.com.emc:storage.Storage.tiny,t,1-naa.5000144f79272808
   Runtime Name: vmhba37:C1:T0:L0
   Device: naa.5000144f79272808
   Device Display Name: EMC iSCSI Disk (naa.5000144f79272808)
   Adapter: vmhba37 Channel: 1 Target: 0 LUN: 0
   Adapter Identifier: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Identifier: 00023d000002,iqn.1992-04.com.emc:storage.Storage.tiny,t,1
   Plugin: NMP
   State: active
   Transport: iscsi
   Adapter Transport Details: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Transport Details: IQN=iqn.1992-04.com.emc:storage.Storage.tiny Alias= Session=00023d000002 PortalTag=1

iqn.1998-01.com.vmware:esxi2-35bac89b-00023d000001,iqn.1992-04.com.emc:storage.Storage.tiny,t,1-naa.5000144f79272808
   Runtime Name: vmhba37:C0:T0:L0
   Device: naa.5000144f79272808
   Device Display Name: EMC iSCSI Disk (naa.5000144f79272808)
   Adapter: vmhba37 Channel: 0 Target: 0 LUN: 0
   Adapter Identifier: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Identifier: 00023d000001,iqn.1992-04.com.emc:storage.Storage.tiny,t,1
   Plugin: NMP
   State: active
   Transport: iscsi
   Adapter Transport Details: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Transport Details: IQN=iqn.1992-04.com.emc:storage.Storage.tiny Alias= Session=00023d000001 PortalTag=1</pre>
<p>This output shows 2 separate devices discovered via different iSCSI IP addresses on my network.  These however, these are the same device!  Clearly ESX knows this, given the screenshot below:</p>
<p><a href="http://www.exaforge.com/wp-content/uploads/2011/07/screen-shot-2011-07-25-at-10-23-12-am.png"><img class="aligncenter size-full wp-image-91" title="Muliple Paths Collapsed" src="http://www.exaforge.com/wp-content/uploads/2011/07/screen-shot-2011-07-25-at-10-23-12-am.png" alt="" width="874" height="197" /></a><br />
Also, much the same view from the commandline:</p>
<pre style="padding-left:30px;">naa.5000144f79272808
   Device Display Name: EMC iSCSI Disk (naa.5000144f79272808)
   Storage Array Type: VMW_SATP_DEFAULT_AA
   Storage Array Type Device Config: SATP VMW_SATP_DEFAULT_AA does not support device configuration.
   Path Selection Policy: VMW_PSP_RR
   Path Selection Policy Device Config: {policy=rr,iops=1000,bytes=10485760,useANO=0;lastPathIndex=1: NumIOsPending=0,numBytesPending=0}
   Path Selection Policy Device Custom Config:
   Working Paths: vmhba37:C1:T0:L0, vmhba37:C0:T0:L0</pre>
<p>So how did ESX determine that these were the same device?  The key is that first line, the 'naa' line.</p>
<p>Every SCSI device that conforms to recent standards (aka the SPC2 standard) supports an INQUIRY command.  This SCSI command allows the client initiator to request some information about any given device.  One of the sections of this information is the Page83h information, also called the Vital Product Data (VPD).  Within that data 'page' is a serial number that is unique for the device.</p>
<p>For the device above, that is the value following the 'naa' string: 5000144f79272808.  (NAA stands for Name Address Authority).</p>
<p>This number is a globally unique identifier assigned by the array vendor to that specific device that is independent of anything else (e.g. LUN #, etc).  This is the key to multipath identification.</p>
<p>So here's what happens.  An ESX host goes an enumerates every LUN visible to a given HBA:</p>
<pre>iqn.1998-01.com.vmware:esxi2-35bac89b-00023d000002,iqn.1992-04.com.emc:storage.Storage.tiny,t,1-naa.5000144f79272808
   Runtime Name: vmhba37:<strong>C1</strong>:T0:L0
   Device: naa.5000144f79272808
   Device Display Name: EMC iSCSI Disk (naa.5000144f79272808)
  <strong> Adapter: vmhba37 Channel: 1 Target: 0 LUN: 0</strong>
   Adapter Identifier: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Identifier: <strong>00023d000002</strong>,iqn.1992-04.com.emc:storage.Storage.tiny,t,1
   Plugin: NMP
   State: active
   Transport: iscsi
   Adapter Transport Details: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Transport Details: IQN=iqn.1992-04.com.emc:storage.Storage.tiny Alias= Session=00023d000002 PortalTag=1</pre>
<p>And then does the next HBA (i've highlighted the differences):</p>
<pre>iqn.1998-01.com.vmware:esxi2-35bac89b-00023d000001,iqn.1992-04.com.emc:storage.Storage.tiny,t,1-naa.5000144f79272808
   Runtime Name: vmhba37:<strong>C0</strong>:T0:L0
   Device: naa.5000144f79272808
   Device Display Name: EMC iSCSI Disk (naa.5000144f79272808)
  <strong> Adapter: vmhba37 Channel: 0 Target: 0 LUN: 0</strong>
   Adapter Identifier: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Identifier: <strong>00023d000001</strong>,iqn.1992-04.com.emc:storage.Storage.tiny,t,1
   Plugin: NMP
   State: active
   Transport: iscsi
   Adapter Transport Details: iqn.1998-01.com.vmware:esxi2-35bac89b
   Target Transport Details: IQN=iqn.1992-04.com.emc:storage.Storage.tiny Alias= Session=00023d000001 PortalTag=1</pre>
<p>Now that it has ID'd each device, it queries the Page83 VPD Serial number we discussed earlier and extracts it. <strong> For every discovered device that has the same serial number as a previously discovered device, the additional device is marked as an additional path to that device.</strong>  You can see that during the 'path claiming' process in the vmkernel logs:</p>
<pre>2011-07-25T17:19:46.458Z cpu1:2667)WARNING: iscsi_vmk: iscsivmk_StartConnection: vmhba37:CH:1 T:0 CN:0: iSCSI connection is being marked "ONLINE"
2011-07-25T17:19:46.458Z cpu1:2667)WARNING: iscsi_vmk: iscsivmk_StartConnection: Sess [ISID: 00023d000002 TARGET: iqn.1992-04.com.emc:storage.Storage.tiny TPGT: 1 TSIH: 0]
2011-07-25T17:19:46.458Z cpu1:2667)WARNING: iscsi_vmk: iscsivmk_StartConnection: Conn [CID: 0 L: 192.168.0.51:53377 R: 192.168.0.7:3260]
2011-07-25T17:19:46.954Z cpu2:2845)ScsiScan: 1098: Path 'vmhba37:C1:T0:L0': Vendor: 'EMC     '  Model: 'LIFELINE-DISK   '  Rev: '1   '
2011-07-25T17:19:46.954Z cpu2:2845)ScsiScan: 1101: Path 'vmhba37:C1:T0:L0': Type: 0x0, ANSI rev: 4, TPGS: 0 (none)
<strong>2011-07-25T17:19:46.955Z cpu2:2845)ScsiScan: 1582: Add path: vmhba37:C1:T0:L0</strong>
<strong>2011-07-25T17:19:46.983Z cpu0:2845)ScsiPath: 4541: Plugin 'NMP' claimed path 'vmhba37:C1:T0:L0'</strong></pre>
<p>Finally, you see the end result:</p>
<pre># esxcli storage nmp device list
naa.5000144f79272808
   Device Display Name: EMC iSCSI Disk (naa.5000144f79272808)
   Storage Array Type: VMW_SATP_DEFAULT_AA
   Storage Array Type Device Config: SATP VMW_SATP_DEFAULT_AA does not support device configuration.
   Path Selection Policy: VMW_PSP_RR
   Path Selection Policy Device Config: {policy=rr,iops=1000,bytes=10485760,useANO=0;lastPathIndex=0: NumIOsPending=0,numBytesPending=0}
   Path Selection Policy Device Custom Config:
   Working Paths: vmhba37:C1:T0:L0, vmhba37:C0:T0:L0</pre>
<p>The system has identified all the relevant paths to a given device and marked them as 'working'.</p>
<p>Going back to the initial post that triggered this discussion, the user had multiple (different) devices that the array was returning with identical VPD serial numbers (a bug in the array software).  Because all of the devices were being returned with the same serial number, ESXi assumed they were all just different paths to the same device.</p>
<p>Now, what happens if your array *doesn't* support these Page83 extensions (maybe it only supports the very basic predecessor information in Page80)?  ESX uses a different method in that case, which will be the subject of a future blog post.</p>
<p>I hope this helped - corrections and comments welcome.</p>
