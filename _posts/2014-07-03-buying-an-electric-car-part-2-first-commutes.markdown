---
layout: post
status: publish
published: true
title: 'Buying an Electric Car: Part 2 - First Commutes'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 517
wordpress_url: http://www.exaforge.com/?p=517
date: '2014-07-03 16:13:44 -0400'
date_gmt: '2014-07-03 23:13:44 -0400'
categories:
- Cars
tags:
- ford
- focus
- electric
- car
comments:
- id: 14516
  author: brad
  author_email: bradwshive@yahoo.com
  author_url: ''
  date: '2014-07-03 17:57:19 -0400'
  date_gmt: '2014-07-04 00:57:19 -0400'
  content: Matt, this is great information, especially about the charger and your
    real world experiences.  Dee and I looked at a leaf before but with all the long
    distance driving and traffic in houston we opted against it and my lack of garage
    keeps us from moving up the EV ladder.
- id: 14523
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-07-03 18:42:39 -0400'
  date_gmt: '2014-07-04 01:42:39 -0400'
  content: Yeah, wouldn't be a good choice for you....its tough without a consistent
    place to charge it.
- id: 18150
  author: Pete
  author_email: peter.gavin@live.com
  author_url: http://vmpete.com
  date: '2014-07-24 07:37:21 -0400'
  date_gmt: '2014-07-24 14:37:21 -0400'
  content: Good post Matt.  The braking score seems odd to me - perhaps because I
    do not know the details of how they are giving a score.  I assume the objective
    is to show how much energy you reclaimed via regenerative braking.  What doesn’t
    make sense is the, as you put it,  “could have recovered”/”Managed to recover”
    measurement.  By that I mean, perhaps the goal in a particular braking event wasn’t
    to stop, but rather to slow down slightly, and that continuing to use the momentum
    of the car during a non-accel, non-decel stage was the most efficient use of its
    stored energy.  After all, the objective is to get from point A to point B..  What
    might also be interesting, considering it’s good indicators of Wh usage, is to
    see how it does in 5 mile speed increments of around highway speeds.  Sort of
    a poor man’s test of Drag Coefficiency of the car, and a reminder of how painful
    wind resistance is to efficiency at higher speeds.  :-).
- id: 31830
  author: mcowger
  author_email: matt@cowger.us
  author_url: ''
  date: '2014-09-15 11:38:07 -0400'
  date_gmt: '2014-09-15 18:38:07 -0400'
  content: |-
    My guess is the scoring looks something like this:

    Ke = (m * v^2) * f

    So they know the mass of the car, and its current velocity, so they can estimate the total kinetic energy in the vehicle.  Multiple that by a fudge factor (f) to factor in the fact that nothing is perfectly efficient, and you can guess the amount of energy that could be recovered and consider that 1%.

    Then, track the amount actually recovered (the sensors can do this easily with a Hall-effect transducer), and calculate a % score.

    In the case you mention, of a partial-stop, the car doesn't ever actually show you or calculate a score, likely in deference to the issue you identify.

    As far as the increment testing - I've considered that, and it certainly would be non linear!  I just don't have enough open, flat free way here in the bay area to do that test!  That being said, I believe the Cd and frontal area of the car are published somewhere, so you could simply calculate it.
---
<p>As I promised in my<a title="Buying an Electric Car: Part 1" href="http://www.exaforge.com/?p=506"> first article </a>on this topic, I'd like to provide a review of my first couple weeks driving and commuting in the car.</p>
<h1>Range</h1>
<p>Many initial comments and questions were about range, and if I was getting the promised 76 miles on a charge that Ford advertises.  Happily, I can say that, on average, I do.  When I drive from my home to my office in either Santa Clara (EMC) or Palo Alto (VMware), with a stop to drop off my daughter at preschool, its a total of 46 miles (yes, really, they are nearly identical).  I can usually get to the office with approx. 56% of battery life remaining, or about 12.8 kWH.  <a href="{{ site.baseurl }}/images/2014/07/focus-electric-screen1.jpg"><img class="alignleft size-medium wp-image-522" src="{{ site.baseurl }}/images/2014/07/focus-electric-screen1-300x184.jpg" alt="focus-electric-screen1" width="300" height="184" /></a>Now, a solid amount of that is the fact that the trip down the hill from my house is about a 1000ft drop over 2 miles, so I get effectively a couple miles for 'free'.  Additionally, the overall trip from Oakland to Santa Clara or Palo Alto is downhill.  As a result, the usage coming back is a bit higher.  The one time I tried it (it was nerve wracking),<strong> I managed to get back into my garage with 1% battery remaining and the car getting increasingly insistent that I needed to charge.  </strong>The total distance for this trip is 90 miles, well in excess of the 76 mile range that Ford claims for the car.</p>
<p><strong>Gamification</strong></p>
<p><img class="alignleft wp-image-523 size-medium" src="{{ site.baseurl }}/images/2014/07/12focuselectric_19-300x222.jpg" alt="2012 Ford Focus Electric" width="300" height="222" />The car gives pretty significant feedback throughout a trip (if you enable it) on your driving style.  As an example, every time you come to a stop, you are given a braking score, indicating how much of the energy you could have recovered you actually managed to recover.  Its a <strong>strong </strong>to get you to drive the way the <a href="{{ site.baseurl }}/images/2014/07/10492221_10203308039075870_8671400667926595085_n.jpg"><img class="alignright size-medium wp-image-524" src="{{ site.baseurl }}/images/2014/07/10492221_10203308039075870_8671400667926595085_n-225x300.jpg" alt="10492221_10203308039075870_8671400667926595085_n" width="225" height="300" /></a>car wants you to drive.  Further, throughout the drive, you can have a constant indicator of Watt-hours (Wh) consumed per mile, which is effectively an indicator of instantaneous efficiency.  This again becomes a game to keep this number as low as possible.  Ford's range claims for the vehicle are based on an average of a little less than 300 Wh/mi, and so if you can achieve better, you get better range.</p>
<p><a href="{{ site.baseurl }}/images/2014/07/Screen-Shot-2014-07-03-at-3.53.20-PM.png"><img class="alignleft size-medium wp-image-526" src="{{ site.baseurl }}/images/2014/07/Screen-Shot-2014-07-03-at-3.53.20-PM-300x268.png" alt="Screen Shot 2014-07-03 at 3.53.20 PM" width="300" height="268" /></a>In addition to the in-car views, the Ford website also shows historical driving information, including charge events.  Most importantly, though, it shows your historical consumption, giving an excellent indication of how you are doing.  As you can see, I average slightly better than Ford's expectation, and am improving.  As a result, my overall range is generally better than Ford's 76 mile estimate.</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p><strong>Charging</strong></p>
<p><a href="{{ site.baseurl }}/images/2014/07/ccs-1-0-24481200-1358451940.jpg"><img class="alignleft wp-image-521 size-medium" src="{{ site.baseurl }}/images/2014/07/ccs-1-0-24481200-1358451940-297x300.jpg" alt="ccs-1-0-24481200-1358451940" width="297" height="300" /></a>The vehicle comes with a 120V, 16 amp charger (more accurately called an Electric Vehicle Service Equipment, because the actual charge circuitry is in the vehicle itself).  At a peak power delivery of only ~1.8kW, however, it takes nearly 20 hours to charge the battery from 0%, which is painfully slow.  My first few days of charging the car at home were disappointing to say the least.  At least once I had to delay a trip in order to let the car charge, which sucked.   Fortunately, the Ford website does at least predict for you when the vehicle will finish charging, and estimate the number of miles you can go at various increments.</p>
<p>As a result, I decided I needed a Level 2 charger that could charge the car more quickly.</p>
<p><strong>Level 2 EVSE</strong></p>
<p><a href="{{ site.baseurl }}/images/2014/07/amazon_base_productimage_new_logo.jpg"><img class="alignright size-full wp-image-520" src="{{ site.baseurl }}/images/2014/07/amazon_base_productimage_new_logo.jpg" alt="amazon_base_productimage_new_logo" width="155" height="219" /></a>I did a bunch of research to identify a Level 2 EVSE (e.g. one that operates on 240V and supplies anywhere between 20 and 60 amps to the car).  Generally, most of the options on the market were in the $800-1200 range for a charger capable of at least 6kW (about 30A).  This felt high to me, for something that is really nothing more than a carefully controlled relay.  As a result, I found the <a title="JuiceBox EVSE" href="http://www.emotorwerks.com/products/online-store/product/show/44-customizable-juicebox-an-open-source-level-2-15kw-ev-charging-station">JuiceBox EVSE</a>, which was a Kickstarter project and a fully open source design.  I ordered the Base kit, which is capable of nearly 15kW of power delivery (2x that of what the 6.6kW charger in the Focus will even attempt to draw) and it was only about $350 given that I assembled it myself.  Assembly involved only stripping+soldering 14 wires, something I've done hundreds of times before, and about 90 minutes of my time.</p>
<p>After I finished the charger and had an electrician come and install a 50A RV plug in my garage (another $300),<strong> I had a fully functional charger, installed, for about $650, where it would have been at least $1100 for a more traditional solution.</strong></p>
<p><a href="{{ site.baseurl }}/images/2014/07/IMG_0163.jpg"><img class="alignright size-medium wp-image-527" src="{{ site.baseurl }}/images/2014/07/IMG_0163-300x225.jpg" alt="IMG_0163" width="300" height="225" /></a>The upside?  The charge can complete in about 3.5 hours, which is very nice.  Since installing the charger, I've never had to postpone a trip or errand.  I know carry the small included charger in the trunk for emergency use.</p>
<p><strong>Continuation</strong></p>
<p>In my next article, I'd discuss charging away from home, the impact of things like electrics and AC/heat, and some of the convenience factors.</p>
