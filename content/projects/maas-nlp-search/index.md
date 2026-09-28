+++
title = 'From Syntax to Semantics: A Natural-Language Interface for Infrastructure Search'
linkTitle = 'MAAS NLP Search'
date = 2025-06-12T17:15:51+01:00
draft = false
featured = true
weight = 3
year = '2022–25'
role = 'Lead UX Designer'
hero = '/images/work-cards/maas-nlp-search.svg'
hook = '3-minute searches. A 2-second fix. Then we removed the syntax entirely.'
description = 'Operators managing thousands of machines waited minutes per query, then still had to hand-translate intent into a set-theory DSL. We made search 60× faster, proved that better syntax help was not enough, and prototyped a natural-language layer that removes the translation step.'
aliases = ['/projects/projectdirectory/maassearch/', '/projects/projectDirectory/maasSearch/']
+++

**TL;DR:** Search in MAAS failed twice: first on speed, then on language. We fixed speed with engineering (2 min → 2 sec per 1,000 machines), tested whether a smarter UI could fix language, but 7 of 11 users still went to the docs, and then prototyped a natural-language layer that writes the query for you.

{{< lens "problem" >}}

MAAS search relied on a custom Domain Specific Language (DSL), precise, but **intimidating and error-prone** for experts and newcomers alike. Two problems were tangled together:

- **Speed.** Environments with 1,800+ machines took **~3 minutes** to load. Every syntax mistake meant another full reload, so power users opened multiple tabs to work around it.
- **Translation.** Users had to convert intent ("deployed machines in us-west-2 with 2–8 TB") into DSL syntax, and they forgot the syntax between sessions. Search was a recurring learning curve, and the docs lived in a different UI entirely.

![MAAS search in 2.9](/images/maas-search/output-onlinegiftools.gif)
*MAAS 2.9 (June 2021): string-based, client-side search over an API that joined multiple tables into one expensive object.*

The query itself hinted at AND/OR logic, but nothing told you which, you had to leave the page and learn it from the documentation.

![Search query](/images/maas-search/search-query.png)

A typical search journey:

![Search journey](/images/maas-search/mermaid-search-journey.png)

**Problem statements we committed to:**
- *[Engineering]* Reduce load time from **2 minutes to 10 seconds for 1,000 machines**, scale shouldn't change the wait.
- *[UX]* Let people **search without learning the syntax**, because the translation step is where most mistakes happen.

**My role:** Lead UX Designer with Engineering and PM. I ran 11 usability tests plus deep-dive interviews, mapped pain points across syntax, performance and query building, and partnered with engineers to prototype both the backend and the UI.

{{< lens "ai" >}}

The core failure was a **translation problem**: humans think in sentences, the system demanded set theory. That's a language task, mapping ambiguous, partial intent onto a formal grammar, which is exactly where natural-language models earn their place.

Just how unforgiving that grammar was made the case. Every machine attribute is an object; a query is a chain of attribute filters and logical operators:

```
ATTRIBUTE(KEY=VALUE|function() [AND|OR|NOT] KEY=VALUE|function() [AND|OR|NOT] RANGE=[MIN,MAX]|{MIN,MAX})

status(type="deployed" AND date=[30-05-2024,12-06-2024]) AND hostname(value=contains("us-west-2")) AND storage(size=["2TB","8TB"])
```

`AND`, `OR`, and `NOT` behave like set operations, so users had to hold Venn diagrams in their heads to know whether a query would over- or under-match:

![Logical operations and operational precedence](/images/maas-search/logicOps.png)

Without brackets, `AND` silently takes precedence over `OR`, so the same clause can return two different result sets:

![Mixing operations with and without brackets](/images/maas-search/logicOpsMix.png)

Values are typed implicitly (bare numbers vs. quoted strings vs. integer-stored dates), inclusive `[ ]` and exclusive `{ }` ranges look almost identical, and storage units hide a 1000-vs-1024 trap, "2TB" and "2TiB" aren't the same number of bytes:

![Working with storage units](/images/maas-search/workingUnits.png)

Every quote, bracket and operator was a place a user could get it wrong, and a place a language layer could get it right on their behalf.

**Why this was the right lever, and not earlier:** AI wouldn't have fixed a 2-minute load time, and it wouldn't have been worth trying before we knew whether better UI alone could close the gap. Only once performance was solved and UI assistance had *measurably* failed (see Judgment) was the translation step isolated as the thing to remove.

**Where the model's job stops:** the language layer resolves to the **existing DSL**, not to a black-box result. The DSL stays the precise, inspectable target; the model only removes the part humans were bad at.

{{< lens "experience" >}}

### Iteration 1: Performance first (MAAS 3.0, 2022)

- Moved filtering and grouping **server-side**
- Reimplemented WebSocket handlers for machine listing
- Refactored DB queries to avoid unnecessary joins

Load time degraded sharply past ~100 machines:

![Machine list metrics](/images/maas-search/machinelist-metrics.png)
![Performance graph](/images/maas-search/maas-performance-graph.png)

We spiked different WebSocket handlers against a 1,000-machine sample database:

![SQLAlchemy timing](/images/maas-search/sqlalchemy.png)
![Handler comparison](/images/maas-search/machine-sqlalchemy-table.png)

### Iteration 2: Syntax assistance

With speed solved, we made the DSL easier to use without leaving the page.

<div style="text-align:center;">
    <img src="/images/maas-search/search-legend.png" alt="Search legend" width="350">
</div>

- **Helper chips** listing available filters and the implicit AND / OR / NOT operators

![Helper chips](/images/maas-search/search-helper.png)

- **Keyboard-first free-text search**, even for values not in the chips

![Free text search](/images/maas-search/free-text-search.png)

- **Edit or remove chips inline**

![Editing chips](/images/maas-search/editing-chips.png)

- **Save, share, and recall** the last 5 searches

![Save search](/images/maas-search/save-search.png)

### Iteration 3: Removing translation entirely

A natural-language layer that converts plain English into valid MAAS DSL, prototyped in **Python + Streamlit**:

- Designed for **ambiguous and partial queries** ("all Ubuntu machines not running")
- Mapped **fallback behaviours** for underspecified requests
- **Feedback loops** resolve ambiguity with the user *before* anything executes

Representative scenarios the prototype was tested against:

| The user says | The layer writes |
| --- | --- |
| "Machines deployed between May 30th and June 12th 2024, in us-west-2, with 2–8 TB of storage." | `status(type="deployed" AND date=[30-05-2024,12-06-2024]) AND hostname(value=contains("us-west-2")) AND storage(size=["2TB","8TB"])` |
| "Any machine with a hostname like sparkiegeek." | `hostname(value=contains("sparkiegeek"))` |
| "Machines running either SSD or NVMe storage." | `storage(type="SSD" OR type="NVMe")` |
| "Storage strictly between 2TB and 8TB, not inclusive." | `storage(size={"2TB","8TB"})` |

<video width="100%" controls>
  <source src="/images/maas-search/maas-search-demo.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

[![GitHub Repo](https://img.shields.io/badge/view-on%20github-blue?logo=github)](https://github.com/amylily1011/NLPsearch), sample code simulating the interaction.

{{< lens "judgment" >}}

- **Sequenced speed before usability.** No search UI can feel good when every mistake costs a two-minute reload. We held back UX work until the engineering goal was met.
- **Fixed the model, not the symptom.** Rather than masking latency in the UI, we pushed filtering server-side and simplified the database model the listing depended on, slower to land, but it removed the ceiling instead of raising it.
- **Didn't declare victory on "feels more efficient."** Iteration 2 tested well on sentiment, but observed behaviour disagreed: users still made translation mistakes and 7 of 11 went back to the docs. We treated the chip UI as a stepping stone, not the answer.
- **Kept what worked.** Save & share survived into the direction because 9 of 11 users said it solved a real pain, they never remembered the syntax.
- **Layered AI over the DSL instead of replacing it.** The DSL stays the source of truth: precise, auditable, and editable. The language layer is only allowed to *propose* a query, and asks when a request is ambiguous rather than guessing.

{{< lens "evidence" >}}

| Signal | Result |
| --- | --- |
| Load time, 1,000 machines | **2 min → 2 sec**, 5× better than the 10-second goal |
| Architecture | Exposed and simplified DB-model inefficiencies for long-term scale |
| Save & share (n=11) | **9/11** said it solved a major pain |
| Syntax assistance alone (n=11) | **7/11** still returned to docs → evidence that syntax help wasn't enough |
| Natural-language prototype | Being tested with MAAS users and other Canonical cloud product teams as a **reusable, cross-product search model** |

Usability was scored 1–5 on efficiency, learnability, error tolerance, and pleasantness:

![Usability results](/images/maas-search/pie-chart.png)

<!-- TODO: add NLP prototype results (query accuracy, time-to-query vs. DSL, adoption) once testing concludes. -->

**What I'd tell another team:** search at scale is equal parts speed, precision and cognitive load. Even "better syntax" isn't enough, **removing syntax** is what unlocks usability, and only a fast backend makes that worth building.
