---
title: "TSP dev blog 2023-02-12 #3 - SonarQube screens with Snyk, Semgrep, and Veracode"
date: "2023-02-12"
original_date: "2023-02-12"
categories:

- "tsp"

---

After significant code refactoring and lots of testing, I wanted to share the updated SonarQube screenshots that bring
together vulnerabilities from different scanners, thanks to TSP. It's the usual suspects: Snyk, SonarQube, Semgrep, and
Veracode.

All scanners are brought together at the same dashboard:

[![screenshot](images/tsp13.png)](images/tsp13.png)

Code review with vulnerabilities shown:

[![screenshot](images/tsp14.png)](images/tsp14.png)

Snyk in SonarQube:

[![screenshot](images/tsp15.png)](images/tsp15.png)

Semgrep is the only scanner so far to detect a `pug` vulnerability:

[![screenshot](images/tsp16.png)](images/tsp16.png)
