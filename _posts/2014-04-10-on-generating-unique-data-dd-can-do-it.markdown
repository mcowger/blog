---
layout: post
status: publish
published: true
title: On Generating Unique Data - 'dd' can do it
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 482
wordpress_url: http://www.exaforge.com/?p=482
date: '2014-04-10 18:14:36 -0400'
date_gmt: '2014-04-10 18:14:36 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p>IDC semi-recently released their <a href="http://idcdocserv.com/241856">guidelines for performance testing</a> on all flash arrays.  Its a good read, and makes solid suggestions for how to handle the unique aspects of all flash arrays, especially ones with dedupe like <a href="http://xtremio.com/">XtremIO</a>, <a href="http://www.purestorage.com/">Pure</a>, etc.</p>
<p>One of their suggestions comes around preconditioning the array (to expose any issues an array or its devices may have with garbage collection after overwrite).  Specifically, they suggest:</p>
<blockquote><p>Our research revealed the best wa y to precondition is to create a significant workload of 4 KB unique, not deduplicatable, or uncompressible exclusive writes and let the test run for 24 hour</p>
<p>&nbsp;</p></blockquote>
<p>This seemed pretty obvious to me...you can't simply write terabytes of zeroes to an array with dedupe/compression and expect to actually occupy terabytes on physical media.  Aaron Skogsberg of Pure suggested this was non trivial, to which I replied:</p>
<p>https://twitter.com/mcowger/status/451068239615758336</p>
<p>To which Aaron replied:</p>
<p>https://twitter.com/DweebiaK/status/451068429554819073</p>
<p>although I will note that he never provided anything to back that up.  A friend of mine, Pat Donahue, also suggested that /dev/urandom wouldn't be a good choice (his timeline is private) because its not as entropic as /dev/random.<br />
In light of those suggestions, I decided to actually back it up.  So, I wrote some quick code that performs the following:</p>
<ol>
<li>Given a file, opens the file and reads it in 4K blocks (to match the dedupe size of most modern dedupe algorithms, including that of XtremIO and others)</li>
<li>For each chunk, calculate its SHA-256 hash, and increment that count in a hashtable of all identified unique chunks.</li>
<li>Report the results</li>
</ol>
<p>In short - do what most dedupe algorithms do.  Granted, this is a simple implementation, but its pretty reasonable to a first approximation.  Further enhancements could use techniques like sliding windows to potentially enhance duplicate identification.  Here's a snippet of the most important function:</p>
<pre style="padding-left: 30px;">def shamethod(percent_of_file=100):
fh = open(filename, 'rb')

blocks_in_file = filesize_bytes / BLOCK_SIZE
blockcount_to_process = int(blocks_in_file * (percent_of_file / 100.0))
blockmap = sorted(random.sample(range(0, blocks_in_file), blockcount_to_process))

prevsize = 0
blocks_processed = 0
sha256_hashes = {}

bar = Bar('Processing File (SHA 256)', suffix='ETA: %(eta_td)s', max=len(blockmap))

for blocklocation in blockmap:
fh.seek(blocklocation * BLOCK_SIZE)
chunk = fh.read(BLOCK_SIZE)
blocks_processed += 1
if chunk:
s = hashlib.sha256()
s.update(chunk)
sha256_hashes[s.hexdigest()] = sha256_hashes.get(s.hexdigest(),0) + 1

print "SHA-256 Results (Baseline)"
print "File Blocks: %s" % blocks_in_file
print "Evaled Blocks: %s" % blockcount_to_process
print "Total Unqiue: %s" % len(sha256_hashes)
print "Dedupability: %s:1" % (round((float(blocks_processed) / len(sha256_hashes)),2)</pre>
<p>From there, I tested this against known dedupe rates from production arrays, and got numbers within 1% of actual values, so I was confident in the implementation.</p>
<p>The next step was to generate my sample data.  I built three sets of data:</p>
<ul>
<li>All zeros: <strong>dd if=/dev/zero of=zeros  count=1048576</strong></li>
<li>Random from <strong>urandom: dd if=/dev/urandom of=urandom  count=1048576</strong></li>
<li>Random from <strong>/dev/random: dd if=/dev/random of=random  count=1048576</strong></li>
</ul>
<p>And ran it through the code:</p>
<pre>→ python fullchecker.py ~/Downloads/zeros 100                                                     ]
Processing File (SHA 256) |################################| ETA: 0:00:00
SHA-256 Results (Baseline)
Percent    :    100
File Blocks:    131072
Evaled Blocks:  131072
Total Unqiue:   1
Dedupability:   131072.0:1
Hashtable (MB): 0.0
Time For SHA256: 7.46s

→ python fullchecker.py ~/Downloads/urandom 100                                                   ]
Processing File (SHA 256) |################################| ETA: 0:00:00
SHA-256 Results (Baseline)
Percent    :    100
File Blocks:    131072
Evaled Blocks:  131072
Total Unqiue:   131072
Dedupability:   1.0:1
Hashtable (MB): 6.0
Time For SHA256: 7.71s

→ python fullchecker.py ~/Downloads/random 100                                                    ]
Processing File (SHA 256) |################################| ETA: 0:00:00
SHA-256 Results (Baseline)
Percent    :    100
File Blocks:    131072
Evaled Blocks:  131072
Total Unqiue:   131072
Dedupability:   1.0:1
Hashtable (MB): 6.0
Time For SHA256: 7.51s</pre>
<p>I've bolded the important parts. As you can see, in the case of all zeroes, we achieved a 100% dedupe rate (as expected) because there is only a single detected unique block. In the case of /dev/random, there were 131072 unique blocks detected out of 131072 total blocks, so the 'best available' random data from /dev/random, achieving a 0% dedupe rate (as is expected).</p>
<p>So, how did /dev/urandom do? It contained 131072 unique blocks out of 131072 total blocks - the <em>EXACT SAME</em> as /dev/random, and demonstrably non-dedupeable.</p>
<p>So - I'm very comfortable in asserting that my original suggestion of using /dev/urandom <strong>does</strong> meet the requirements in the IDC paper of unique data.</p>
<p>A note about comments: I'm open to constructive comments demonstrating that my work above isn't valid or needs changes, but I <strong>will</strong> delete comments that don't make a technical case for it or if they turn in <em>ad-hominum</em> or <em>ad-comitatus</em> suggestions.</p>
