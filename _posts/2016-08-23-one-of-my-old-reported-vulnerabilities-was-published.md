---
title: "One of my old reported vulnerabilities was published: CVE-2014-8733"
date: "2016-08-23"
categories:
  - "hadoop"
  - "infosec"
---

CVE-2014-8733:
[https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-8733](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-8733)

It was fun to work with Hadoop security in 2014... This vuln was a tricky one
because I was responsible for Hadoop managed service platform security, and our
clients had SSH access to Hadoop cluster nodes in some cases.

If I remember correctly, fix wasn't easy - required release of new CDH version
which moved configuration parameters between files (world readable access was
required for Hadoop client to function). And then, several months later, it
reappeared again after another patch.

<blockquote class="twitter-tweet" data-lang="en"><p dir="ltr" lang="en">I missed that one of my reported old CVEs was finally published: <a href="https://t.co/1wRygqPbRR">https://t.co/1wRygqPbRR</a> Low risk, it caused some problems with compliance</p>— Mikhail Samoylenko (@m_samoylenko) <a href="https://twitter.com/m_samoylenko/status/767870513922318338">August 22, 2016</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

