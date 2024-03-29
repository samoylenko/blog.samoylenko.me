---
title: "TSP dev blog 2022-06-22 - exports to DefectDojo and SARIF"
date: "2022-06-22"
original_date: "2022-06-22"
categories:

- "tsp"

---

Today, I worked on exporting issue reports in 
[DefectDojo Generic Findings format](https://defectdojo.github.io/django-DefectDojo/integrations/parsers/#generic-findings-import)
and [SARIF](https://sarifweb.azurewebsites.net/).

Even though the POC worked as expected, what I experienced there made me rethink
the priority for these items. Both formats are surprisingly complex and
confusing, and I think they are doing the opposite of what this project (TSP) is
trying to achieve, which is simplicity... With dozens of fields for each issue,
I don't think it'll be easy for any downstream consumer, whether a machine or a
human, to consume and process that complex data. The [previous experience with
SonarQube](../2022-06-09-tsp-002) was much more productive.

So I am leaving this work finished only at the basic functionality state, i.e.,
I got DefectDojo export working, but I am dropping any work on SARIF support for
now and instead prioritizing the research on what would be the meaningful
downstream consumers of the TSP automated issue reports, before spending more
time on additional export formats. But, of course, this decision is not final,
and customers will be able to request this feature in the future.

[![screenshot](images/tsp05.png)](images/tsp05.png)
