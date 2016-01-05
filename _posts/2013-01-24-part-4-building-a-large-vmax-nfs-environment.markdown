---
layout: post
status: publish
published: true
title: 'Part 4: Building A Large VMAX & NFS Environment'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 318
wordpress_url: http://www.exaforge.com/?p=318
date: '2013-01-24 09:26:58 -0500'
date_gmt: '2013-01-24 17:26:58 -0500'
categories:
- Uncategorized
tags:
- configuration
- Symmetrix
- tdat
- tdev
- vg8
- VMAX
- VMware
comments:
- id: 107
  author: 'Part 5: Building A Large VMAX &amp; NFS Environment | VMs Are Free, Right?'
  author_email: ''
  author_url: http://www.exaforge.com/2013/01/25/part-5-building-a-large-vmax-nfs-environment/
  date: '2013-01-25 06:04:23 -0500'
  date_gmt: '2013-01-25 14:04:23 -0500'
  content: '[...] &larr; Part 4: Building A Large VMAX &amp; NFS&nbsp;Environment
    [...]'
---
<p>In previous parts of this series, I've described the requirements for this project, as well as the overall design features of restrictions of the VG8 gateway and VMAX parts of this design. In this part, we'll go in depth to the configuration required to accomplish the design on the VMAX. This will be very far in the weeds, so prepare yourselves!</p>
<p><em>(note: While I prepared this script for use with solutions enabler, the customer decided to do this work in Unisphere for VMAX (the web GUI). As a result, this set of scripts and commands are untested, and probably contain a couple minor syntax errors. Consider them as a guide, not a recipe.)</em></p>
<p><strong>BIN File</strong></p>
<p>The first setup step for any Symmetrix system is the creation of the BIN file, which defines to the system all of its features, available parts (like disks) and basic configuration. I'll agree up front this is a bit of an anachronism compared to other systems (even our own VNX). Nonetheless, it exists mostly because it enables EMC to squeeze the last tiny bit of configurability and performance from the system. It also allows EMC to predefine for customers, if they like, the configuration of volumes for certain use cases. We've made use of that here.</p>
<p><strong>Default Devices</strong></p>
<p>The first thing we've defined in this BIN file are the TDAT devices which make up the pools of storage that will be used later. While this can be done by the customer at any time later, we chose to do it here, in this step to speed up the customer's deployment process. Specifically, nearly 100% of every drive in the system (EFD, FC&lt; and SATA included) has been allocated via TDAT devices. We choose the number of the TDAT devices per disk based on some internal best practices and restrictions, but generally we follow the rule that we like to see ~8-12 TDAT virtual devices for EFD and FC devices, and around 25 for SATA drives. Note: These are special 'TDAT' devices, and exist solely to back the storage pools. We will create customer-sized devices later as thin devices (called TDEVs)</p>
<p>Also, because we knew that this entire frame would be used exclusively by the VG8 gateway, we also precreated the ~6 devices required for the gateway to store its configuration data (about 60GB worth of devices).</p>
<p><strong>Pool Creation</strong></p>
<p>First, we create and name the pools of disk we will need using a symconfigure script:</p>
<pre>--- createpool.txt ---
 create pool FC15_600GB_R10,type=thin;
 create pool EFD_400GB_3R5,type=thin;
 create pool SATA_2TB_R10,type=thin;
 ---
 symconfigure -sid XXXX -f createpool.txt commit</pre>
<p>Note that at this time the pools are empty - they have no actual storage backing them. We'll correct that next:</p>
<pre>--- fillpools.txt ---
 add dev 144:783 to pool FC15_600GB_R10, type=thin, member_state=ENABLE;
 add dev 784:A33 to pool EFD_400GB_3R5, type=thin, member_state=ENABLE;
 add dev A34:C13 to pool SATA_2TB_R10, type=thin, member_state=ENABLE;
 ---
 symconfigure -sid XXXX -f fillpools.txt commit</pre>
<p>But wait, you say? Where did those hex numbers come from? Well, for those of you without Symmetrix experience, those are the hex identifiers we use for identifying volumes. Rather than just calling them LUN 340, we call it 0x154 (or 154 for short). In fact, these are the identifiers of the pre-created TDATs in the BIN file I described above. If we pre-create them, we know what the IDs will be, and can provide them to the customer for preparation. Most of the configuration lines above are self-explanatory - member_state=ENABLE causes the device to be enabled for use by the pool as soon as they are added. Some customers might not want them to be auto enabled (in case they have more to add in a short time, for example), so we don't presume.</p>
<p><strong>AutoMeta Configuration</strong></p>
<p>Symmetrix has a limit of single devices of 240GB (yes, this is also a bit anachronistic). This is a bit small for most uses, and so it also supports the creation of larger virtual devices by combining these smaller devices together. This technique is called a Meta or a MetaLun.</p>
<p>Now, the user can manually create those if they wish, but manually creating devices and combining them is a pain inthe butt, and easily repeatable, so lets let the computer do it. By enabling 'autometa', we simply specify a size of device over which the system should create these meta devices on its own, and a couple parameters under which it should do so:</p>
<pre>--- autometa.txt ---
set auto_meta=ENABLE;
set auto_meta_config=STRIPED;
set auto_meta_member_size=262668 CYL;
---
symconfigure -sid XXXX -f autometa.txt commit</pre>
<p>To break this down a bit:</p>
<ul>
<li>"set auto_meta=ENABLE" - This enables the autometa system as a whole</li>
<li>"set auto_meta_config=STRIPED" - All new metas should be written to in a striped fashion, rather than concatnated together. This is a performance option that should almost always be true.</li>
<li>"set auto_meta_member_size=262668 CYL" - metas should be created using 262668 cylinder parts. I could have just as easily specified this in GB, but I wanted to use the absolute maximum value (this happens to equate to approx 240 GB)</li>
</ul>
<p><strong>TDEV Creation</strong></p>
<p>Now we need to create the actual devices that our VG8 gateway will store data on. We ended up deciding on 480 GB devices (more on how we go to that number in a future part of the series, but suffice to say that number struck a good balance between performance and manageability). In total, we will need 476 of these devices (again, how we got there <a href="http://www.exaforge.com/2013/01/22/part-2-building-a-large-vmax-nfs-environment/">was explained in part 2</a>).</p>
<pre>--- createtdevs.txt ---
create dev count=476,size=525336 CYL,emulation=CELERRA_FBA,config=TDEV,binding to pool=FC15_600GB_R10,preallocate size=200 GB;
bind tdev C15:C1A to pool FC15_600GB_R10,preallocate size = ALL;
---
symconfigure -sid XXXX -f createtdevs.txt commit</pre>
<p>Allow me to break this one down as well:</p>
<ul>
<li>create dev count=476 - create 476 of the following device</li>
<li>size=525336 CYL - They should be this size. This happens to be 480GB. I could have specified it in GB, but again I am a control freak.</li>
<li>emulation=CELERRA_FBA - Devices that will be used by a VG8 gateway need some special care and options set, and this is basically a macro that sets them easily.</li>
<li>config=TDEV - create these as thin devices</li>
<li>binding to pool=FC15_600GB_R10 - bind these to the FC drive pool. By default new writes will go to this pool (unless FAST moves the relevant blocks to a different pool)</li>
<li>preallocate size=200 GB - preallocate 200GB of each device. We know that these will get used, so we preallocate a little less than half the space as a small initial performance boost.</li>
<li>The second line binds the pre-created control volumes to the same pool, so that they have backing.</li>
</ul>
<p>With that, we've brought up our array with its BIN file, create the pools of storage and created the 476 thin devices needed. When the customer performed this task using the GUI (as a complete Symmetrix newbie), this part of the process took him about 20 minutes. Using this command line, this would probably take about 10 minutes.</p>
<p><strong>Masking &amp; Device Presentation</strong></p>
<p>Next, we want to get the VG8 to see these devices we've created.  Note, because we presented the control devices directly to the ports using the BIN file, we dont have to deal with them at this time.</p>
<p>VMAX uses a mechanism similar to VNX's host groups/storage groups concept to present devices, but with significantly more flexibility.   To present devices, we create 'masking views', which are composed of initiator groups (host WWPNs), port groups (through which the hosts should access the storage) and storage groups (the devices that should be accessed).  Once we create these three constructs and link them together in a masking view, the VG8 will be able to access the storage.</p>
<p><strong>Initiator Groups</strong></p>
<p>Initiator groups are pretty easy to create with the command line (and via GUI):</p>
<p>We simply create the initiator record itself with a reasonable name and with the consistent_lun option (which guarantees that all initiators from the same host will see a given storage device on the same LUN value.</p>
<pre>symaccess -sid XXXX -name Server2_IG -type initiator -consistent_lun create</pre>
<p>Then we add each of the actual WWPNs from the server to the record:</p>
<pre>symaccess -sid XXXX -name Server2_IG -type initiator -wwn 50:06:01:60:47:20:XX:YY add
symaccess -sid XXXX -name Server2_IG -type initiator -wwn 50:06:01:61:47:20:XX:YY add</pre>
<p>We repeat this process for all 8 datamovers in the VG8.</p>
<p><strong>Port Groups</strong></p>
<p><a href="http://www.exaforge.com/2013/01/22/part-2-building-a-large-vmax-nfs-environment/">I mentioned in part 2</a> how we planned to zone the ports of the VG8 gateway to the FA ports on the VMAX.  We simply use the same information here to inform the VMAX of the decisions made.</p>
<pre>symaccess -sid XXXX  -name Server2_PG -type port -dirport  1e:0, 3e:0, 14e:0, 16e:0, 2e:0, 4e:0, 13e:0, 15e:0 create</pre>
<p>Here we've specified a portgroup for server2 with 8 total ports (4 for each of the 2 HBAs in the datamover).  Again, repeat this for all 8 datamovers.</p>
<p><strong>Storage Group</strong></p>
<p>Now, we create the storage group.  Because all datamovers in a cluster should see the same devices, we need only a single storage group.</p>
<pre>symaccess -sid XXXX -name ServerALL_SG -type storage devs 0C2C:E08</pre>
<p>So, we create a storage group using the range nomenclature; C2c:E08 means 'include all device between C2c and E08, inclusive, in the group.  Now how did I know the device numbers?  When I created the devices above using 'create dev', symconfigure output the new device range as part of its completion notice.</p>
<p>We'll also create a storage pool that contains just the control volumes and the gatekeper as well:</p>
<pre>symaccess -sid XXXX -name ServerALL_CV -type storage devs C15:C1A</pre>
<p><strong>Masking Views</strong></p>
<p>Lastly, lets create masking views to link all these together.</p>
<pre>symaccess -sid XXXX create view -name Server2_View -sg ServerALL_SG -pg Server2_PG -ig Server2_IG -lun 010 -celerra</pre>
<p>Here we've specified that we want a new view called Server2_View using the storage, port and initiator groups we've created.  We also specify which LUN # to start at (VG8 requires you start at 010) and that the system is a 'Celerra' (the older name for VG8).  Once this command finishes executing, server2 is able to see all its data storage.  Simply repeat this process for the remaining 7 datamovers.</p>
<p>Next we'll add the control LUNs:</p>
<pre>symaccess -sid XXXX create view -name Server2_View_CV -sg ServerALL_CV -pg Server2_PG -ig Server2_IG -lun 00 -celerra</pre>
<p>And repeat for all the datamovers.</p>
<p><strong>Tiering</strong></p>
<p>The last step is to setup the tiering mechanisms, because we want the system to use its EFD, FC and SATA drives to their best possible use.  As it stands at this moment, nothing will ever move from FC, because tiering is not enabled.  Let's fix that.</p>
<p><strong>Tier Identification</strong></p>
<p>First, we need to tell the system which pools to use for tiering, and what they are.  Because we only have 3 pools, they should all be used for tiering, this is pretty easy:</p>
<pre>symtier -sid XXXX create -name FC15_600GB_R10_TIER -tgt_raid1 -technology FC -vp
symtier -sid XXXX create -name EFD_400GB_3R5_TIER -tgt_raid5 -technology EFD -vp
symtier -sid XXXX create -name SATA_2TB_R10_TIER -tgt_raid1 -technology SATA -vp</pre>
<p>To break down the first line:  We want to create a tier called FC15_600GB_R10_TIER which should use only pools that are RAID1 and FC disks that are VP pools (there are other kinds of non-VP pools, but they are rarely used).</p>
<p>Next, we enable the FAST system altogether, and allow it to move data automatically as it sees fit.  Its possible to require a human to approve all moves, but to me, that kind of defeats the purpose:</p>
<pre>symfast -sid XXXX enable -vp
symfast -sid XXXX set -control_parms -approval_mode AUTO_APPROVE
symfast -sid XXXX set -control_parms -vp_data_move_mode AUTO 
symfast -sid XXXX set -control_parms -vp_allocation_by_fp ENABLE</pre>
<p>Third, we create a FAST policy using the tiers from above.  By default, a policy created using three 3 tiers will allow any and all movement of data between tiers without limits.  You can set limits (for example, to prevent dev/test from chewing up too much EFD), but in this case, its not needed.</p>
<pre>symfast -sid XXXX -fp create -name PA_VG8_01
symfast -sid XXXX -fp add -tier_name FC15_600GB_R10_TIER
symfast -sid XXXX -fp add -tier_name EFD_400GB_3R5_TIER
symfast -sid XXXX -fp add -tier_name SATA_2TB_R10_TIER</pre>
<p>Last, we associate our storage group with this policy.  Upon association, we've told the array to apply the data movement rules specified in the policy to the storage group, and to begin work.</p>
<pre>symfast -sid XXXX associate -sg ServerALL_SG -fp_name PA_VG8_01 -priority 2</pre>
<p>Note that we did not assign a fast policy to the Control Volume devices, because those are required (as per the manual) to stay on FC mirrored drives.</p>
<p>And with that, we are 100% complete carving and allocating storage to the VG8 system.  All told, this process took a newbie administrator with no previous symmetrix experience less than an hour to complete, including significant discussion about what the options mean.  If I were to do it using just command line, it would likely be closer to 30 minutes.</p>
