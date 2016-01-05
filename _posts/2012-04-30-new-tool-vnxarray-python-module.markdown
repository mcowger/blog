---
layout: post
status: publish
published: true
title: 'New Tool: VNXArray Python Module'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 239
wordpress_url: http://www.exaforge.com/?p=239
date: '2012-04-30 10:07:29 -0400'
date_gmt: '2012-04-30 17:07:29 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 68
  author: bjossig
  author_email: bjossi.gunnarsson@gmail.com
  author_url: http://gravatar.com/bjossig
  date: '2012-10-25 12:09:01 -0400'
  date_gmt: '2012-10-25 20:09:01 -0400'
  content: The link is broken
---
<p>After being inspired by <a href="http://www.velemental.com">Clint's</a> work, I decided to built something very similar - a Python module.  It connects with naviseccli to any given VNX array, collects 90% of the relevant data and compiles it into a set of true Python objects.  Why Python?  Because its better than PoSH, because its cross platform :).  I plan to extend this further with information about tiers, etc, but this is a start.</p>
<pre><span style="font-family:Courier;">myArray = VNXArray("1.1.1.1","sysadmin","sysadmin,0,True) print myArray.controllerPair.model ------ VNX5500</span></pre>
<p><span style="font-family:Courier;">or, a little more complex:</span></p>
<pre><span style="font-family:Courier;">for disk in myArray.disks: print disk;</span></pre>
<pre>Disk
 {'ID': u'Bus 0 Enclosure 0  Disk 0',
 'capacityMB': u'274845',
 'currentSpeed': u'6Gbps',
 'driveType': u'SAS',
 'lun': u'77 81 ',
 'maximumSpeed': u'6Gbps',
 'numLuns': u'2',
 'numReads': u'7535989',
 'numWrites': u'4122963',
 'private': True,
 'productId': u'ST930060 CLAR300',
 'productRevision': u'FF03',
 'raidGroupId': u'0',
 'serial': u'3SE2DTK5    ',
 'state': u'Enabled',
 'type': u'77: RAID1/0 81: RAID1/0 ',
 'userCapacity': u'5',
 'vendorId': u'SEAGATE'}
 Disk
 {'ID': u'Bus 0 Enclosure 0  Disk 1',
 'capacityMB': u'274845',
 'currentSpeed': u'6Gbps',
 'driveType': u'SAS',
 'lun': u'77 81 ',
 'maximumSpeed': u'6Gbps',
 'numLuns': u'2',
 'numReads': u'7413955',
 'numWrites': u'4138719',
 'private': True,
 'productId': u'ST930060 CLAR300',
 'productRevision': u'FF03',
 'raidGroupId': u'0',
 'serial': u'3SE2DR0Y    ',
 'state': u'Enabled',
 'type': u'77: RAID1/0 81: RAID1/0 ',
 'userCapacity': u'5',
 'vendorId': u'SEAGATE'}</pre>
<pre>for lun in myArray.luns: print lun;</pre>
<pre>LUN
 {'Name': u'multipiece datastore lun_2',
 'RAIDType': u'N/A',
 'capacityMB': 10240,
 'currentOwner': u'SP A',
 'defaultOwner': u'SP A',
 'number': u'56',
 'prctBound': 100,
 'prctRebuilt': 100,
 'private': True,
 'raidGroupID': u'N/A',
 'state': u'Bound',
 'uid': u'60:06:01:60:34:50:29:00:16:BC:FD:54:6A:71:E1:11'}</pre>
<p>Get it here:</p>
<p><a href="http://www.exaforge.com/wp-content/uploads/2012/04/VNXArray.py_.zip">VNXArray.py.zip</a></p>
