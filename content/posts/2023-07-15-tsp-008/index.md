---
title: "TSP dev blog 2023-06-26 #5 - SARIF: From denial to TSP's internal standard"
date: "2023-07-15"
original_date: "2023-07-15"
categories:

  - "tsp"

---

When I started working on the project, I researched [SARIF](https://sarifweb.azurewebsites.net), but it was only
supported by a few security tool vendors and looked like a [total nightmare to work with](../2022-06-22-tsp-003).

But I am on a
[constant lookout to ensure that I am not reinventing the wheel](https://samoylenko.me/site/appsec/index.html#sources-of-application-security-issues),
so I kept coming back to SARIF again and again, including experimental support, and read all the docs several times but
kept declining the central role of the standard in what I am doing, even though it did look logical to use research by
people that are way smarter than me.

Well, the last drop was when I learned that [Snyk Code](https://snyk.io/product/snyk-code/) uses SARIF as its primary
export format (even if you choose "JSON" over "SARIF," it still uses SARIF, and that makes a lot of sense).

After finishing my work on Snyk Code automation for the project, it looks like all work around SARIF adoption is done,
and it now totally makes sense to use it to replace the custom
[TSP generic issue format](https://docs.scanproject.io/site/standalone/format-generic.html).

So I am planning another major refactoring of the project code, replacing my custom generic issue format with SARIF in
all the project code internals.
