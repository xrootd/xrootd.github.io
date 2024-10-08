---
layout: default
title:  Home Page
tab:    home
---

Welcome to the XRootD webpage
-----------------------------

The XROOTD project aims at giving high performance, scalable fault tolerant
access to data repositories of many kinds. The typical usage is to give
access to file-based ones. It is based on a scalable architecture, a
communication protocol, and a set of plugins and tools based on those. The
freedom to configure it and to make it scale (for size and performance)
allows the deployment of data access clusters of virtually any size, which
can include sophisticated features, like authentication/authorization,
integrations with other systems, WAN data distribution, etc.

XRootD software framework is a fully generic suite for fast, low latency
and scalable data access, which can serve natively any kind of data,
organized as a hierarchical filesystem-like namespace, based on the concept
of directory. As a general rule, particular emphasis has been put in the
quality of the core software parts.

Quick Links
-----------
 * [LICENSE](COPYING.LGPL.txt) - XRootD is distributed under the terms of the
   GNU LGPL License
 * [Policies](policies/SWRSP.htm) - Software Release and Support Policy
 * [WLCG Privacy Notice](https://wlcg-docs.web.cern.ch/policy/data_privacy/WLCGPrivacyNotice2022.pdf) - recommended privacy policy for sites using XRootD
 * [Scalla-Intro.pdf]({{ site.url }}/papers/Scalla-Intro.pdf) - an overview of the xrootd
   system
 * {{ "" | latest_release_url }} - the latest version of the software
 * [Release Notes](https://github.com/xrootd/xrootd/releases) - a list of significant changes
   between the releases
 * [GitHub repository](https://github.com/xrootd/xrootd) - repository browser
 * [GitHub bug tracking](https://github.com/xrootd/xrootd/issues) - report and
   track bugs

News
----

 * Known [issues](news/R5.5.0-Issues.htm) in Release 5.5.0
 * Release 4.x series end of life is September 1st, 2021!
 * Software Release and Support Policy [explained](policies/SWRSP.htm)!

\[[RSS](/rss.xml)\]

<ul>
{% for post in site.posts %}
 <li> <span class="post_date">[{{ post.date | date_to_string }}]</span> <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
