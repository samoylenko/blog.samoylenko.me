---
title: "TSP dev blog 2023-06-26 #4 - ScanProject.io is now live!"
date: "2023-06-26"
original_date: "2023-06-26"
categories:

  - "tsp"

---

It took me a while to update the blog, but if, for some reason, you are following the TSP (The Scan Project) progress
here, it's time to check out <https://scanproject.io> and <https://docs.scanproject.io>!

Oh, and I totally revisited the [decision about SARIF and DefectDojo support](../2022-06-22-tsp-003). Both are
well-supported now :)

In short, the [free basic version of the project is available to all
now](https://hub.docker.com/r/scanproject/standalone). [Semgrep Open Source](https://semgrep.dev/), [SonarLint](https://www.sonarsource.com/products/sonarlint),
and [Dependency Check](https://owasp.org/www-project-dependency-check) are embedded and ready to use. But it doesn't
have any advanced issue processing - just puts everything together in
a [generic report](https://docs.scanproject.io/site/standalone/format-generic.html) ([that is still quite useful](https://docs.scanproject.io/site/standalone/report-formats.html)).

I won't say anything about the paid version yet.
