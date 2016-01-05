---
layout: post
status: publish
published: true
title: Using VMware IO Analyzer
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 183
wordpress_url: http://www.exaforge.com/2011/12/15/using-vmware-io-analyzer/
date: '2011-12-15 09:02:36 -0500'
date_gmt: '2011-12-16 01:02:36 -0500'
categories:
- VMware
tags: []
comments: []
---
<p>As a general storage nerd, I'm always interested in new benchmarking tools, especially if they can make it easy to help get the numbers people need.</p>
<p>VMware’s new fling (e.g. unsupported experimental software) called IO Analyzer is pretty cool. Quickly, its a preconfigured virtual machine that gives you a web interface to run performance benchmarks using IOmeter (excellent software) and collect the relevant performance statistics, all in 1 clean interface. I like it.</p>
<p>While it does come with instructions, I thought I'd put up a post on how to install it and run through a first benchmarking session.</p>
<ul>
<li>First, <a href="http://labs.vmware.com/flings/io-analyzer">download the IO analyzer OVA from VMware’s Flings site</a>.</li>
<li>Next, deploy the OVA just like any other Virtual Appliance:</li>
</ul>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996440.jpg" alt="myWPEdit Image" /><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996454.jpg" alt="myWPEdit Image" /></p>
<p>As the installation instructions state, select <strong>thick </strong>provisioned disks for the best performance evaluation:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996505.jpg" alt="myWPEdit Image" /><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996518.jpg" alt="myWPEdit Image" /></p>
<ul>
<li>Once the virtual machine appliance has been uploaded and powered on, give it about 2 minutes to properly boot for the first time. Then, open the virtual machine’s console:</li>
</ul>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996577.jpg" alt="myWPEdit Image" /></p>
<p>If you are like me, you don’t have a DHCP server on your network, so you will need to setup manual networking. If you do have a DHCP server and the VM picked up a reasonable IP address that can access your ESXi hosts, then skip ahead :). If not, follow along:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996663.jpg" alt="myWPEdit Image" /></p>
<p>After the networking is configure, you <strong>MUST login to the console </strong>of the VM,<strong> or nothing will work. Do so now</strong> using the default password and username (<strong>root</strong> / <strong>vmware</strong>). You will end up with a grey screen – this is normal:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996791.jpg" alt="myWPEdit Image" /></p>
<p>Now, open up your web browser and shoot over to the IP address of your VM (note, don’t use the URL that the text GUI spits out, it doesn’t work):</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996878.jpg" alt="myWPEdit Image" /></p>
<p>Click on the Setup/Run section:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996894.jpg" alt="myWPEdit Image" /></p>
<p>Add the hostname or IP address of the host that your appliance is running on, along with credentials to access. IO Analyzer needs to used this in order to run resxtop against that host during the test to collect data. Then, add the VM’s information:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323996981.jpg" alt="myWPEdit Image" /></p>
<p>The key here is to select a Workload spec that matches your needs. I tend to like the ones listed at the bottom, specifically the 8k and 4k OLTP workloads – they are reasonable realistic for server type workloads. If you have a different workload (e.g. VDI), you'll need to choose something else. Developing a workload spec is something we'll tackle in a future article).</p>
<p>Enter a reasonable run duration (at least 2 minutes – preferably more like 30 minutes) and hit Run</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323997103.jpg" alt="myWPEdit Image" /></p>
<p><small>￼</small></p>
<p>While the test is running, if you are super curious, you can take a look at the VM’s console and watch IOmeter running:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323997155.jpg" alt="myWPEdit Image" /></p>
<p>I'd advise against playing with it while its running :)</p>
<p>When it completes, the appliance will let you know:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323997191.jpg" alt="myWPEdit Image" /></p>
<p>Do as it says and click the results tab:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323997203.jpg" alt="myWPEdit Image" /></p>
<p>And click View Summary for your test:</p>
<p><img src="http://www.exaforge.com/images/2011/12/mywpedit_image_1323997217.jpg" alt="myWPEdit Image" /></p>
<p>Excellent! As you can see, my LUN was able to support 25,000 IOPs at about 100MBit on a Database workload. Great to know! Also, we can see that it basically only used vmhba3, which is a good hint for me that I need to check my patching and ensure I've got proper balancing setup (PowerPath/VE to the rescue!).</p>
<p>Hope you enjoy!</p>
