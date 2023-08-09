---
title: "TSP dev blog 2023-08-09 #6 - SARIF: new era"
date: "2023-08-09"
original_date: "2023-08-09"
categories:

  - "tsp"

---

Wow. Just WOW. Switching to [SARIF](https://sarifweb.azurewebsites.net/) instantly eliminated the need for a 30% custom
code to support custom tools' formats.
The following scanners embed nice and well without any need for any extra code now:

* Snyk Code (This one uses [SARIF](https://sarifweb.azurewebsites.net/) everywhere, but I won't tell anybody)
* Snyk Open Source
* Semgrep

It worked like a charm. I guess I am a new [SARIF](https://sarifweb.azurewebsites.net/) supporter/champion now :)

Scan Project major refactoring is still on the way, but that's mostly the paid version
stuff - [the free scanner](https://docs.scanproject.io/site/standalone/quickstart.html) still
works, and the image gets updated daily with new [DependencyCheck](https://github.com/jeremylong/DependencyCheck/)
vulnerability databases. No one will ever notice the
switch to [SARIF](https://sarifweb.azurewebsites.net/) there ([Semgrep Open Source](https://semgrep.dev/pricing)), but it's quite an achievement :)
