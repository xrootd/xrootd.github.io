---
layout: default
title:  Home Page
tab:    home
---

## XRootD: eXtended ROOT Daemon

The [XRootD](https://xrootd.org) project provides a high-performance,
fault-tolerant, and secure solution for handling massive amounts of data
distributed across multiple storage resources, such as disk servers, tape
libraries, and remote sites. It enables efficient data access and movement in a
transparent and uniform manner, regardless of the underlying storage technology
or location. It was initially developed by the High Energy Physics (HEP)
community to meet the data storage and access requirements of the BaBar
experiment at SLAC and later extended to meet the needs of experiments at the
Large Hadron Collider (LHC) at CERN. XRootD is the core technology powering the
[EOS](https://eos-web.web.cern.ch/) distributed filesystem, which is the storage
solution used by LHC experiments and the storage backend for
[CERNBox](https://cernbox.web.cern.ch/). XRootD is also used as the core
technology for global CDN deployments across multiple science domains.

XRootD is based on a scalable architecture that supports multi-protocol
communications. XRootD provides a set of plugins and tools that allows the user
to configure it freely to deploy data access clusters of any size, and which can
include sophisticated features such as erasure coded files, various methods of
authentication and authorization, as well as integration with other storage
systems like [ceph](https://ceph.io).

## Quick Links

 * [GitHub repository](https://github.com/xrootd/xrootd) - repository browser
 * [GitHub bug tracking](https://github.com/xrootd/xrootd/issues) - report and track bugs
 * [LICENSE](COPYING.LGPL.txt) - XRootD is distributed under the terms of the GNU LGPL License
 * [Policies](policies/SWRSP.htm) - Software Release and Support Policy
 * [WLCG Privacy Notice](https://wlcg-docs.web.cern.ch/policy/data_privacy/WLCGPrivacyNotice2022.pdf) - recommended privacy policy for sites using XRootD
 * [Release Notes](https://github.com/xrootd/xrootd/releases) - a list of significant changes between the releases

## News

<ul>
{% for post in site.posts limit:30 %}
 <li> <span class="post_date">[{{ post.date | date_to_string }}]</span> <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

\[[RSS](/rss.xml)\]
