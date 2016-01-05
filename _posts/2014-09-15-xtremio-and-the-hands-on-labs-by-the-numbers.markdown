---
layout: post
status: publish
published: true
title: 'XtremIO and the Hands-on Labs: By The Numbers'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 534
wordpress_url: http://www.exaforge.com/?p=534
date: '2014-09-15 13:41:14 -0400'
date_gmt: '2014-09-15 20:41:14 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 31981
  author: Tom Queen
  author_email: twqueen@gmail.com
  author_url: ''
  date: '2014-09-16 07:50:33 -0400'
  date_gmt: '2014-09-16 14:50:33 -0400'
  content: Cool stuff, Matt. Thanks for sharing. Love the bit about iSCSI too. What
    pushed you all in that direction vs. FC? Cost:Performance primarily? As one who
    has never had issue with iSCSI, it's neat to see it perform in a demanding environment.
- id: 32854
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-09-19 16:39:47 -0400'
  date_gmt: '2014-09-19 23:39:47 -0400'
  content: The push was really about infrastructure ease and cost.  Those UCS systems
    already have a ton of 10GbE thats convenient, so we just wired it up that way.  Perf
    tests show it was fine.
---
<p><a href="{{ site.baseurl }}/images/2014/09/IMG_2587.png"><img align="right" class="alignright size-medium wp-image-545" src="{{ site.baseurl }}/images/2014/09/IMG_2587-300x105.png" alt="IMG_2587" width="300" height="105" /></a></p>
<p>One of my single most popular posts last year was some detail about the storage infrastructure of the VMworld HoL, so this year I'd like to do even a bit more depth and more data.</p>
<p style="color: #000000;">This year, roughly 80% of the HoL were powered by Cisco UCS servers and EMC XtremIO storage. Now, that number varies by how you measure (do you count based on labs provisioned, used? Number of servers connected?), but either way you slice it, its somewhere in the ~80% range. The remainder were serviced by technical preview EVO:RACK systems .</p>
<h3 style="font-weight: bold; color: #000000;"><strong>By The Numbers</strong></h3>
<ul style="color: #000000;">
<li>Datacenters: 3 (Santa Clara, CA (us20), Wenatchee, WA (us03), Amsterdam)</li>
<li>Total Hosts: 318</li>
<li>Total XtremIO Arrays: 7 (5 active)</li>
<li>Total Virtual Machines Deployed (entire week): &gt; 103,000<a href="{{ site.baseurl }}/images/2014/09/IMG_2587.png"><br />
</a></li>
<li>Maximum Concurrent Labs Deployed: &gt; 425</li>
<li>Maximum VMs Deployed: 4,100</li>
</ul>
<p style="color: #000000;"><strong>Wenatchee, WA</strong>: 172 hosts, 332 sockets, 45TB memory<br />
<strong>Santa Clara</strong>: 106 hosts, 210 sockets, 35TB memory<br />
<strong>Amsterdam</strong>: 40 hosts, 80 sockets, 20TB memory</p>
<h3 style="font-weight: bold; color: #000000;"><strong>Storage Connectivity</strong></h3>
<p style="color: #000000;">This year, for the first year, the storage subsystem was connected entirely via iSCSI rather than fiberchannel. Combine this with the iSCSI connectivity used by the vCloud Air team, and I think its pretty clear that iSCSI can be used even for the most demanding workloads.</p>
<h3 style="font-weight: bold; color: #000000;"><strong>Storage Design</strong></h3>
<p style="color: #000000;">The OneCloud team (the team that runs the HoL infrastrucure) worked closely with the EMC team and we came to a final design of 7 LUNs of 4TB each assigned to each 10-host cluster. Besides configuration of the iSCSI environment, there's very little to actually be decided on an XtremIO system...you choose the LUN size and thats about it. The content addressing architecture consistently spreads the workload as needed across all the SSDs in the system, and the XDP protection technology keeps everything safe.</p>
<p style="color: #000000;">Sure makes a storage admin's life <del>boring</del> easy when there are so few things that need to be configured to get optimal performance.</p>
<h3 style="font-weight: bold; color: #000000;"><strong>Storage Performance</strong></h3>
<p style="color: #000000;">This year, I was able to grab significant performance data from the environment to analyze, and I found it fascinating. Some other time I'll post the Python code I wrote to actually drag this information from vCenter Operations.</p>
<p style="color: #000000;">Here's an example:</p>
<p><a href="{{ site.baseurl }}/images/2014/09/Screen-Shot-2014-09-12-at-10.59.21-AM.png"><img class="wp-image-543 size-large" src="{{ site.baseurl }}/images/2014/09/Screen-Shot-2014-09-12-at-10.59.21-AM-1024x713.png" alt="us03-xms1 IOPs" width="584" height="406" /></a> <br>us03-xms1 IOPs</p>
<p style="color: #000000;">We can see here this cluster (in us03, or Wenatchee) generally pushed around 20K IO/s (this is one of the 3 active X-bricks in the datacenter). In other words, given that these bricks are easily capable of 150K+ IO/s, <strong>this array never even got pushed to half its capability.</strong> During the show and the tours that the OneCloud team gave, they repeatedly stated that they expect to halve the infrastructure next year.  <strong>After 2 years of 100% storage availbility</strong> on the part of the XtremIO systems, they're comfortable increasing the size of the failure domain.</p>
<p style="color: #000000;">To go with that IO/s metric, we also have the throughput metric:</p>
<p><a href="{{ site.baseurl }}/images/2014/09/Screen-Shot-2014-09-12-at-10.59.10-AM.png"><img class="wp-image-541 size-large" src="{{ site.baseurl }}/images/2014/09/Screen-Shot-2014-09-12-at-10.59.10-AM-1024x778.png" alt="us03-xms1 throughput" width="584" height="443" /></a> <br>us03-xms1 throughput</p>
<p style="color: #000000;">Now this one is measured in KB/s, so its a little harder to read. However, if you look at the y-axis labels on the left, you'll see they are in the hundreds of thousands. In other words, 400k on that graph represents about 400MB/sec. So the array in question seemed to sustain just shy of 400MB/sec throughput with briefly higher spikes into the gigabytes/sec range. Again - not even close to making this array breathe hard.</p>
<p style="color: #000000;">The last image I'd like to share is the latency measurements for all of this:</p>
<p><a href="{{ site.baseurl }}/images/2014/09/Screen-Shot-2014-09-12-at-10.59.16-AM.png"><img class="wp-image-542 size-large" src="{{ site.baseurl }}/images/2014/09/Screen-Shot-2014-09-12-at-10.59.16-AM-1024x768.png" alt="us03-xms1 latency" width="584" height="438" /></a> <br> us03-xms1 latency</p>
<p style="color: #000000;">Again, you can see that with the exception of a couple brief spikes up to 3-4ms, <strong>the system stayed below 500µs (half a millisecond!) </strong>for end to end IO (that includes time spent on the network!) for the entire show.  Pretty impressive.</p>
<p>At first I was a bit confused, because 3-4ms (even very briefly) would be well outside what we expect from an XtremIO. I realized after a few moments, however, that this latency is measured on the *datastore* level, meaning it includes any latency introduced in the host iSCSI stack and both directions of the network (in and out).</p>
<p style="color: #000000;">When I analyzed those brief latency spikes (4 measurements out of 506,219 collected for that array, or 0.00007%), I found signicantly higher network latency as well, meaning the network was the most likely cause of the latency. This is fascinating - and something that only vCenter Operations with its collection-at-scale could provide insight into.</p>
<p style="color: #000000;">Every other cluster showed these same patterns of moderate usage and ultra low latency.</p>
<p style="color: #000000;">I'd love to hear your comments, as well as take requests for specific data/analysis, because I've got a ton of data and have only scratched the surface.</p>
<p style="color: #000000;">Finally, I'd like to thanks Patrick Noia (OneCloud), Josh Schnee (Performance Engineering) Pablo Roesch and Joey Dieckhans (Tech Marketing) for all their help in getting this data!</p>
<p style="color: #000000;">Today's post was brought to you by Python 2.7.6, vCenter Operations and <a href="http://pygal.org">pygal</a></p>
