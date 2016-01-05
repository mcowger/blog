---
layout: post
status: publish
published: true
title: Unisphere for VMAX API & Carbon/Graphite
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 370
wordpress_url: http://www.exaforge.com/?p=370
date: '2013-03-12 10:14:35 -0400'
date_gmt: '2013-03-12 18:14:35 -0400'
categories:
- Uncategorized
tags: []
comments:
- id: 122
  author: Sam
  author_email: simplesam1047@yahoo.com
  author_url: ''
  date: '2013-04-25 11:23:23 -0400'
  date_gmt: '2013-04-25 19:23:23 -0400'
  content: "I really need your help. I cannot make a request to emc sevrer to get
    the data back in json format. The URL I am using is 'https://myhost:8443/univmax/restapi/performance/BEDirector/keys?symmetrixId=1234'
    but this is not working in web browser. I tried from Linux command prompt also
    via \n'curl -X GET -Gk -u username:password -d symmetrixId=1234 -H \"Content-Type:
    application/json\" https://myhost:8443/univmax/restapi/performance/BEDirector/keys'\n\nI
    have been stuck for 2 days. Any help would be appreciated. Also the link in your
    script to Array/metrics does not work when put in the browser. Plus from schema
    I think it (Array/metrics) expects a param.\n\nI am a DBA , hence my knowledge
    of web technologies is limited. Kindly help. Thanks !"
- id: 123
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-04-25 11:49:05 -0400'
  date_gmt: '2013-04-25 19:49:05 -0400'
  content: |-
    Sam,

    Part of the problem is that when you run the requests from your browser or curl is that you are using a GET request, where the API requires a POST request with a bunch of parameters in the form of JSON or XML.

    You can't simply plug it into a browser, you need to download the python code and run it in an appropriate environment.
- id: 124
  author: Sam
  author_email: simplesam1047@yahoo.com
  author_url: ''
  date: '2013-04-25 12:46:47 -0400'
  date_gmt: '2013-04-25 20:46:47 -0400'
  content: Let me try your python code. I will have to download some libraries, but
    let me try it and get back. Thanks for replying quickly.
- id: 125
  author: Sam
  author_email: simplesam1047@yahoo.com
  author_url: ''
  date: '2013-04-25 13:32:54 -0400'
  date_gmt: '2013-04-25 21:32:54 -0400'
  content: |-
    ‘curl -X POST -Gk -u username:password -d symmetrixId=1234 -H “Content-Type: application/json” https://myhost:8443/univmax/restapi/performance/BEDirector/keys‘

    above should work, as it does a post and i pass the symmetrixId as a param. But it still fails. setting up python code would be little hard as i need to install libraries + we are more of perl users.
- id: 126
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-04-25 13:34:13 -0400'
  date_gmt: '2013-04-25 21:34:13 -0400'
  content: No, the above SHOULDN'T work, because you haven't included any JSON text
    , which is required (that or XML).
- id: 127
  author: Sam
  author_email: simplesam1047@yahoo.com
  author_url: ''
  date: '2013-04-25 18:41:06 -0400'
  date_gmt: '2013-04-26 02:41:06 -0400'
  content: "curl -i -X POST -k -u username:password -H \"Content-Type: application/json\"
    https://myhost:myport/univmax/restapi/performance/BEDirector/keys -d '{\"symmetrixId\":\"1234\"}'\n\nUsing
    above i get \nRoot name 'symmetrixId' does not match expected ('beDirectorKeyParam')
    for type [simple type, class com.emc.em.restapi.performance.dto.BeDirectorKeyParam]\n\nAny
    help ? Some problem with the type, i cannot understand anything from the schema.
    I know you have recommended I try in python but i know wvwn if it works that wont
    help as our solution."
- id: 128
  author: mattcowger
  author_email: matt@cowger.us
  author_url: http://www.exaforge.com
  date: '2013-04-26 07:11:12 -0400'
  date_gmt: '2013-04-26 15:11:12 -0400'
  content: |-
    You still aren't making the request properly.

    Take a look at my code - look at all the JSON that needs to happen:

    requestObj = {'arrayParam':
                {'endDate': int(time.time()*1000), #End time to specify is now.
                 'startDate': int(time.time()*1000)-(3600*1000), #start time is 60 minutes before that
                 'metrics': ['IO_RATE'], #array of what metrics we want
                 'symmetrixId': '000194900728' #symmetrix ID (full 12 digits)
                }
              }

    You aren't specifying most of that, and those are ALL required parameters.
- id: 129
  author: Sam
  author_email: simplesam1047@yahoo.com
  author_url: ''
  date: '2013-04-26 07:43:32 -0400'
  date_gmt: '2013-04-26 15:43:32 -0400'
  content: matt - I was able to get this working from CURL. The thing I was doing
    wrong above was passing the -d in wrong format. Thanks for the help.
- id: 1384
  author: Dherla
  author_email: dimitri.herla@nrb.be
  author_url: ''
  date: '2013-09-25 08:52:01 -0400'
  date_gmt: '2013-09-25 08:52:01 -0400'
  content: "Hello,\r\n\r\nI try the same but I have the following message.\r\nI execute
    the following request :\r\nhttps://servername:8443/univmax/restapi/performance/Array/metrics\r\nAvec
    les paramètres suivants :\r\n{\r\n\"arrayParam\":\r\n{\r\n\"startDate\":\"1379635200000\",\r\n\"endDate\":\"1379721600000\",\r\n\"symmetrixId\":\"000194900728\",\r\n\"metrics\":[\"IO_RATE\"]\r\n}\r\n}\r\nI
    have the following response :\r\nhttp Status : 501 =&gt; Not Implemented\r\nResponse
    :\r\nThe server does not support the functionality required to fulfill the request\r\n\r\nIs
    it a problem in my request or on the server ?\r\n\r\nThanks in advance"
- id: 1385
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-09-25 15:41:30 -0400'
  date_gmt: '2013-09-25 15:41:30 -0400'
  content: "Are you sure you have the correct version of Unisphere installed?   Need
    1.5.1 or higher \n\nAlso, you will need to fill in your own correct Symmetrix
    serial number - the default won't work (as that's my serial number).  "
- id: 2244
  author: winn
  author_email: winnjohnston@gmail.com
  author_url: ''
  date: '2013-12-10 17:39:25 -0500'
  date_gmt: '2013-12-10 17:39:25 -0500'
  content: "I am trying to get a starttime and endtime for RESPONSE_TIME_READ of only
    60 Seconds. However the script you have doesn't like it very much. Can you please
    elaborate on why?\r\n\r\nThanks\r\n-winn"
- id: 2245
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2013-12-10 17:41:37 -0500'
  date_gmt: '2013-12-10 17:41:37 -0500'
  content: I've found that timespans less than 5 minutes seem to be an issue.  I've
    taken to simply specifying a 5+ minute range and only taking the most entry entry
    from the returned set of values.
- id: 3324
  author: JS
  author_email: jslabonte@gmail.com
  author_url: ''
  date: '2014-04-03 18:10:57 -0400'
  date_gmt: '2014-04-03 18:10:57 -0400'
  content: "Hi, \r\n\r\nVery good job, thank you.\r\nAlthough the API or calls seem
    flaky at best. It works, but I get http \"500\" errors a lot (20% of times).\r\n\r\nAny
    ideas what could be happening ?"
- id: 3369
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-04-10 17:21:04 -0400'
  date_gmt: '2014-04-10 17:21:04 -0400'
  content: I've seen this too, and I'm not sure the source.  I've filed a bug, but
    more customer reports always helps...you should file one too :)
- id: 14291
  author: Ricardo Fuentes
  author_email: ricardo@oncrono.com
  author_url: http://jirah.org
  date: '2014-07-02 12:17:46 -0400'
  date_gmt: '2014-07-02 19:17:46 -0400'
  content: "Dude,\r\n\r\nThank you for this implementation. You really help me out.
    Now I can get real stats from my VMAX :-)\r\n\r\nCheers."
---
<p>Recently, a customer asked me how they could get performance data from their Symmetrix array into Graphite, their metrics graphing tool of choice.</p>
<p>I originally went straight to my old standby of 'symstat', which is good, but doesn't have an easy-to-parse output.  Unlike many other SYMCLI commands, it doesn't have an XML output, so it becomes quite an exercise to parse the data.  Nontheless, I did it, and you can find the file here:</p>
<p><a href="https://github.com/mcowger/randompython/blob/master/symmcarbon.py">https://github.com/mcowger/randompython/blob/master/symmcarbon.py</a></p>
<p>However, I decided I'd rather use the newly-available Unisphere for VMAX REST API.  I found some interesting things.  You'll need Unisphere for VMAX 1.5.1 or later to use this.</p>
<ul>
<li><span style="line-height:16px;">While we call this a REST API, its a lot closer to a SOAP API.  Oh well.</span></li>
<li>The Programmer's Guide is here: http://bit.ly/Y6BvO9</li>
<li>The XSD and sample Java can be found here: <a href="https://download.emc.com/downloads/DL46151_Unisphere-for-VMAX-REST-API-1.5.1.zip">https://download.emc.com/downloads/DL46151_Unisphere-for-VMAX-REST-API-1.5.1.zip</a></li>
<li>Its pretty fast.</li>
<li>It supports XML or JSON.  Kinda nice.  Python does JSON serialization/deserialization very well, so I went that route.</li>
</ul>
<p>Without further ado, here is a script that pulls the total system IOPs from the Symmetrix and pushes it into Carbon/Graphite using the MetricLineReceiver method.</p>
<p><a href="https://github.com/mcowger/randompython/blob/master/symmREST.py">https://github.com/mcowger/randompython/blob/master/symmREST.py</a></p>
<p>&nbsp;</p>
