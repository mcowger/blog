---
layout: post
status: publish
published: true
title: Python + CloudFoundry = ❤
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 574
wordpress_url: http://www.exaforge.com/?p=574
date: '2014-12-04 10:07:45 -0500'
date_gmt: '2014-12-04 18:07:45 -0500'
categories:
- Uncategorized
tags: []
comments: []
---
<p><a href="{{ site.baseurl }}/images/2014/12/Twitter_CF_Avatar_128x128_400x400.png"><img align="left" src="{{ site.baseurl }}/images/2014/12/Twitter_CF_Avatar_128x128_400x400.png" alt="Twitter_CF_Avatar_128x128_400x400" width="242" height="242" /></a> <p>As I've been getting more and more into CloudFoundry's infrastructure side (and not just its awesomeness as a developer <strong>using</strong> it), I've of course been wanting to automate it.</p>
<p><img align="right" src="{{ site.baseurl }}/images/2014/12/python.sh-600x600.png" alt="python.sh-600x600" width="242" height="242" />
	<P>
	
	The cf cli is very good, and quite reliable, but it doesn't have XML or JSON output, and really, the <a href="http://apidocs.cloudfoundry.org">CF API i</a>s quite accessible and pretty clear.  So I decided I'd like to use Python to do it of course.  I searched PyPI and found a <a href="https://pypi.python.org/pypi/python-cloudfoundry/0.3">python-cloudfoundry project</a> but noted that it only worked with the CF 1.0 release, not the current v2 API releases.</p>
<p>So because of the awesomeness of the open source community and the CF API documentation, I began to rewrite it.</p>
<p>I'm pleased to announce the <a href="https://github.com/mcowger/python-cloudfoundry">first alpha version of python-cloudfoundryv2 is available on my GitHub account. </a> Check it out, please share feedback and send me some pull requests.</p>
<p>Here's an example of how to use it:</p>

{% highlight python %}

from cloudfoundry import CloudFoundryInterface
import logging
from pprint import pprint,pformat


logging.basicConfig(format='%(asctime)s: %(levelname)s %(module)s:%(funcName)s@%(lineno)d | %(message)s', level=logging.CRITICAL)

def test_app(username,password,api):
    cfi = CloudFoundryInterface(target='api.run.pivotal.io',username=username,password=password,debug=False)
    cfi.login()
    myapp = cfi.get_app_by_name("chargers")
    pprint(myapp)
    pprint(cfi.spaces, cfi.routes)
    new_app = cfi.create_app("api-test-app",cfi.spaces.keys()[0])
    pprint(new_app)


{% endhighlight %}