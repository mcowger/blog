---
layout: post
status: publish
published: true
title: 'Finding: Docker May Increase Your Memory Requirements'
author:
  display_name: mcowger
  login: mcowger
  email: matt@cowger.us
  url: ''
author_login: mcowger
author_email: matt@cowger.us
wordpress_id: 556
wordpress_url: http://www.exaforge.com/?p=556
date: '2014-10-13 06:56:36 -0400'
date_gmt: '2014-10-13 13:56:36 -0400'
categories:
- Uncategorized
tags: []
comments: []
---
<p><img class="alignright size-full wp-image-558" src="{{ site.baseurl }}/images/2014/10/docker-logo-loggedout.png" alt="docker-logo-loggedout" width="161" height="38" />I've been experimenting with <a href="https://www.docker.com">Docker</a> lately (like <a href="http://www.google.es/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=4&amp;cad=rja&amp;uact=8&amp;ved=0CDIQFjAD&amp;url=http%3A%2F%2Fthenewstack.io%2Fdocker-and-cloud-foundry-open-source-projects-are-defined-by-ownership%2F&amp;ei=Idc7VK3IHNLOaJuVgMgF&amp;usg=AFQjCNGwE2pqyZirEDFXZeRZCk8HgR5jYQ&amp;bvm=bv.77161500,d.d2s">much</a> of <a href="http://purevirtual.eu/2014/08/12/simple-how-to-install-for-panamax-docker-management-on-os-x/">the world</a>) and specifically I wanted to try running this site (my blog, and some ancillary applications) using it as a place to start.</p>
<p>I figured this would be a perfect place to start, as the software involved (Wordpress, nginx, php-fpm, mysql) are all reasonably lightweight and easy. Additional, there are <a href="https://www.google.es/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=1&amp;cad=rja&amp;uact=8&amp;ved=0CCMQFjAA&amp;url=https%3A%2F%2Fregistry.hub.docker.com%2F&amp;ei=XNc7VPWDOYLcapXPgeAJ&amp;usg=AFQjCNF8F9GBc8TlyzMqnJODp6pvKScyRw&amp;bvm=bv.77161500,d.d2s">Docker Registry containers</a> available for all of these.</p>
<p><img class="alignleft wp-image-559 size-full" src="{{ site.baseurl }}/images/2014/10/Screen-Shot-2014-10-13-at-3.54.40-PM.png" alt="Screen Shot 2014-10-13 at 3.54.40 PM" width="430" height="104" />I run my site on a simple <a href="https://www.digitalocean.com">DigitalOcean</a> droplet, and historically have used either the 512M size, which has been great.</p>
<p>So, for this round, I decided to deploy a 1GB CoreOS droplet (which is designed for running nothing but Docker images), as it has pretty low resource utilization (among many other benefits).</p>
<p>What I discovered was interesting. I deployed the <a href="https://registry.hub.docker.com/_/wordpress/">Wordpress</a> docker image, which went fine. I then deployed <a href="https://registry.hub.docker.com/_/mysql/">MySQL</a> image, which went fine as well. Lastly, I deployed an nginx reverse proxy then tried to start everything up. What I found was that not everything could start. I could either start wordpress and nginx, or wordpress and mysql or nginx &amp; mysql, but not all three at once....I ran out of memory.</p>
<p>I felt like this was odd, because this was the exact same software I had been running on a 512MB droplet before under CentOS. I did a bit more digging, and found that this problem stems from the very nature of Docker Hub and how it works.</p>
<p>Each Docker image uses layered filesystems to progressively build an application up from the base OS (which could be CentOS, Ubuntu, etc) to add the application, its requirements and then its configuration.</p>
<p>Now, this seems great, until you realize that there is no standardization for these lower layers.</p>
<p>As an example, the ctlc/wordpress Dockerfile is based on ctlc/apache-php, which itself is based on ubuntu:trusty. mysql &amp; ngnix are based on debian:wheezy. ctlc/wordpress also installs perl, and adds a apt-get -update command. The mysql image does a apt-get-update as well, but nginx does not.</p>
<p>All of this is hidden from casual inspection (you have to go check the Dockerfile). All of this means that, effectly, each of these Docker images/containers is running a different set of files &amp; more importantly libraries...and so we have 3 copies of effectively-the-same-but-slightly-different residing in memory.</p>
<p>In theory, Docker can handle some of this with with equivalent of VMware's page sharing, but only if:</p>
<ol>
<li>You use the same base images (debian:wheezy, or ubuntu:trusty) and</li>
<li><a href="http://developerblog.redhat.com/2014/09/30/overview-storage-scalability-docker/?utm_content=buffer8a955&amp;utm_medium=social&amp;utm_source=twitter.com&amp;utm_campaign=buffer">You use aufs</a> (which CoreOS doesn't do by default - it uses btrfs)</li>
</ol>
<p>So, if you use CoreOS as distributed today and use Docker Hub images (as most examples on the web do), you wont have the benefit, and are very likely to run into the same scenario I did where running with Docker requires more memory than without in a single-host environment.</p>
<p>An interesting caveat...and it was enough for me to decide that I wanted to save the memory (thus saving money) moreso than the deployment benefits of Docker.  Certainly, I could have gone and deployed aufs-based Docker base operating rather than CoreOS, and I could have rolled my own Docker images....and I would probably do that in a corporate environment.  But here, my whole focus was ease of use.</p>
<p>Now, I'm not an expert on Docker - I could have missed something here.  If I did, please let me know.</p>
