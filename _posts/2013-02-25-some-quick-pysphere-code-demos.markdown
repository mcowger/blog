---
layout: post
status: publish
published: true
title: Some Quick pySphere Code Demos
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 368
wordpress_url: http://www.exaforge.com/?p=368
date: '2013-02-25 13:17:05 -0500'
date_gmt: '2013-02-25 21:17:05 -0500'
categories:
- Uncategorized
tags: []
comments:
- id: 121
  author: Adel Rafiq
  author_email: aadilsr@gmail.com
  author_url: ''
  date: '2013-03-08 11:06:25 -0500'
  date_gmt: '2013-03-08 19:06:25 -0500'
  content: Awesomeness!
- id: 1265
  author: stephen
  author_email: spainton@iprosystems.com
  author_url: ''
  date: '2013-08-16 19:26:21 -0400'
  date_gmt: '2013-08-16 19:26:21 -0400'
  content: |-
    The first set of code just gives me the MOR id's.
    Is there a way to translate these to the actual values?
- id: 1517
  author: winn
  author_email: WINN_JOHNSTON@yahoo.com
  author_url: ''
  date: '2013-11-07 22:45:35 -0500'
  date_gmt: '2013-11-07 22:45:35 -0500'
  content: |-
    for vmpath in vmlist:

                    vm = s.get_vm_by_path(vmpath)
                    mor = vm._mor
                    counterValues = pm.get_entity_counters(mor)
                    print vmpath
                    stats = pm.get_entity_statistic(mor, [12])
    #               cpuready = counterValues['cpu.ready']
                    print "VM Name",vm.get_property("name")
                    print " cpuready", stats
                    print "---"
- id: 3293
  author: rushabh sanghvi
  author_email: rushabh268@gmail.com
  author_url: ''
  date: '2014-03-26 21:08:17 -0400'
  date_gmt: '2014-03-26 21:08:17 -0400'
  content: "I get the VM using vm = server.get_vm_by_path(vmpath)\r\nHow do I get
    the name? and then how do I delete that VM?"
- id: 3370
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-04-10 17:22:05 -0400'
  date_gmt: '2014-04-10 17:22:05 -0400'
  content: |-
    You'll need to search through the inventory to find the VM's name.

    You can use deletevm_task() to delete a VM.
- id: 3371
  author: rushabh sanghvi
  author_email: rushabh268@gmail.com
  author_url: ''
  date: '2014-04-10 17:38:18 -0400'
  date_gmt: '2014-04-10 17:38:18 -0400'
  content: How to search the inventory to find the VM's name? Can I get all the names
    in a list or so?
- id: 34308
  author: mike smith
  author_email: gsaray101@yahoo.com
  author_url: ''
  date: '2014-09-24 12:34:37 -0400'
  date_gmt: '2014-09-24 19:34:37 -0400'
  content: "Hi, I used your first code to pull vm guest perf data, but it seems very
    slow. Would it be possible to speed this script or any thoughts around it?\r\n\r\nRegards,\r\n\r\nMike"
- id: 34930
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-10-03 18:28:59 -0400'
  date_gmt: '2014-10-04 01:28:59 -0400'
  content: There are definitely ways to speed it up, but it gets very complex very
    quickly....and it solidly depends on how slow/fast your vcenter server and database.
---
<p>A couple requests came in over the internal EMC vSpecialist lists last week to demonstrate 2 things with the vSphere API.</p>
<p>The first was a request for a demonstration of how to extract individual VM performance disk performance data.  Here's a python script using the <a href="https://code.google.com/p/pysphere/">pysphere library</a> to accomplish that:</p>
<div>
<h6 style="padding-left:60px;"># Import the pysphere library (makes python talk to vsphere)</p>
<p>from pysphere import VIServer<br />
from pysphere.resources import VimService_services as VI</p>
<p>#Create an object to work with<br />
server = VIServer()</p>
<p>#Connect to the server<br />
server.connect("IP.ad.dre.ss", "Administrator", "AReallyStrongPassword")</p>
<p>#Get the list of all VMs (their VMX paths) in the vCenter that are powered on.<br />
vmlist = server.get_registered_vms(status='poweredOn')</p>
<p>#For each path....<br />
for vmpath in vmlist:<br />
#Get the current performance manager object (it changes, so we can't just instatiate it once)<br />
pm = server.get_performance_manager()<br />
#Get an actual VM object from the path<br />
vm = server.get_vm_by_path(vmpath)<br />
#Get the managed object reference for the VM, because the performance manager only accepts MoRefs<br />
mor = vm._mor<br />
#Get all the counters and their current values for that VM.<br />
counterValues = pm.get_entity_counters(mor)<br />
#Do some quick math on the values.<br />
#They come to us in a convienent dictionary form.<br />
#Values are descrobed here: http://www.vmware.com/support/developer/vc-sdk/visdk41pubs/ApiReference/virtual_disk_counters.html<br />
IOPs = counterValues['virtualDisk.numberReadAveraged'] + counterValues['virtualDisk.numberWriteAveraged']<br />
BandwidthKBs = counterValues['virtualDisk.read'] + counterValues['virtualDisk.write']<br />
ReadLatency = counterValues['virtualDisk.totalReadLatency']<br />
WriteLatency = counterValues['virtualDisk.totalWriteLatency']<br />
#print them out.<br />
print "VM Name",vm.get_property('name')<br />
print " IOPs",IOPs<br />
print " Bandwidth(KB/s):",BandwidthKBs<br />
print " Read Latency (ms):",ReadLatency<br />
print " Write Latency (ms):",WriteLatency<br />
print "-------"</h6>
<p>The next was a request to show the datastore overprovisioning rate:</p>
<h6 style="padding-left:60px;">
from __future__ import division<br />
#Use Python 3.0's division (default to long division, not integer)<br />
from pysphere import *<br />
# Import the pysphere library (makes python talk to vsphere)</h6>
<h6 style="padding-left:60px;">#Create an object to work with<br />
server = VIServer()</h6>
<h6 style="padding-left:60px;">#Connect to the server<br />
server.connect("IP.addy.goes.here", "Administrator", "P@ssword1!")</h6>
<h6 style="padding-left:60px;">for ds, name in server.get_datastores().items():<br />
#Get all the datastores and their names.<br />
props = VIProperty(server, ds)<br />
#Create a object that contains all the properties<br />
try:<br />
capacity = props.summary.capacity<br />
free = props.summary.freeSpace<br />
uncommitted = props.summary.uncommitted<br />
#Per the API docs: "Total additional storage space, in bytes, potentially used by all virtual machines on this datastore. The server periodically updates this value."<br />
used = capacity - free<br />
overprov = used + uncommitted<br />
#Combine whats currently used with what could be used.<br />
overprovPercent = (overprov / capacity) * 100<br />
print "Datastore", name<br />
print " ","Capacity (MB) : ",capacity / 1024 / 1024<br />
print " ","Free (MB) : ",free / 1024 / 1024<br />
print " ","Used (MB) : ",used / 1024 / 1024<br />
print " ","OverProvisioning (MB) : ",overprov / 1024 / 1024<br />
print " ","OverProvisioning % : ",overprovPercent<br />
except AttributeError:<br />
pass<br />
#Not all datastores have this property (NFS, for example), so we put it in a try/catch to prevent errors.</h6>
<p>&nbsp;</p>
</div>
