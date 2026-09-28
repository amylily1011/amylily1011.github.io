+++
title = 'Redesigning MAAS: A Scalable Infrastructure Layout That Became the Design System'
linkTitle = 'MAAS Application Layout'
date = 2021-10-01T14:53:41Z
draft = false
featured = true
weight = 7
year = '2021'
role = 'UX Lead'
hero = '/images/work-cards/maas-application-layout.svg'
hook = '9 in 10 misclicked. Six iterations later, the layout became the design system.'
description = "MAAS 2.8 buried actions far from context and hid real-time status, so operators trusted the CLI over the UI. Six tested iterations produced a side-nav, contextual-action layout that was promoted into Canonical's Cloud & Infra design system."
aliases = ['/projects/projectdirectory/applicationlayout/', '/projects/projectDirectory/applicationLayout/']
+++

**TL;DR:** The MAAS UI didn't scale: to 1,800 machines, to networking concepts, or to operators who trusted the CLI more. In six weeks and six iterations we rebuilt navigation, action placement, and status visibility, and other teams pivoted to the result before it was even finished.

{{< lens "problem" >}}

MAAS 2.8's UI **struggled to scale**. Navigation was unintuitive, actions sat far from their context, and real-time status was hard to see. At the same time, the layout had to become a foundation for the wider Cloud & Infra product family.

**How might we** create an application layout that improves learnability, efficiency, and error tolerance; scales to **100–1,800+ machines**; and can serve as the division's design system?

**My role:** UX Lead with Engineering and PM, 5 internal and 1 external interview, a survey, card sorting, and usability tests across **6 design iterations**.

### Who we were designing for

- **35.5%** production private cloud · **64.6%** lab/homelab · **18.2%** both

![MAAS user groups](/images/maas-application-layout/usergroups.png)

- Power users opened **multiple tabs** to offset slow loads (up to **3 minutes** for 1,800+ machines)
- ~**30%** were **CLI-first**, using the UI only for bulk tasks

> "I only use the UI for bulk actions because I cannot do that in the CLI, but the CLI is more reliable."

### What was broken

**Navigation & IA.** The top bar looked clean but didn't scale; nested submenus caused misclicks. In card sorting, networking concepts (fabric, VLAN, subnets, spaces) clustered poorly, **9 of 10** participants misclicked finding logs and networking.

![Old navigation](/images/maas-application-layout/old-nav.png)
![Card sorting 1](/images/maas-application-layout/card-sorting-1.png)
![Card sorting 2](/images/maas-application-layout/card-sorting-2.png)

**Action placement.** Every action hid behind one green button, far from the machine it acted on.

![Action button](/images/maas-application-layout/action-button.png)

**Layout & feedback.** A centred grid constrained table space, and status and errors didn't appear where people looked, one reason users trusted the CLI more.

{{< lens "ai" >}}

*Retrospective: this 2021 work predates AI in the product. Here's the lever I'd reach for now.*

The research exposed a **reality gap**: when commissioning or deployment failed, the UI could say *that* it failed but not *why*, so operators went to logs and the CLI. That gap, turning raw logs into a plain-language cause and a next step, **shown where the user is already looking**, is a strong fit for a language model.

The layout built the right slots for it. The state-aware Action Bundle already knows what a machine can do next, and the Status Bar is where failure lands. An explanation placed there would close the gap without adding yet another screen.

{{< lens "experience" >}}

We prototyped five components, **Side Navigation, Action Bundle, Status Bar, Quick Access List,** and **IA changes**, and tested each.

**What worked ✅**
- **Side navigation**, less left-right scanning, clearer grouping
- **Action Bundle**, contextual actions cut click complexity (5 of 6 praised it)
- **Status Bar**, commissioning and deploy progress visible without switching pages
- **Quick Access List**, fast machine switching while debugging

**What needed work ⚠️**
- Quick Access items "jumped" mid-action, risky in large lists
- At 100–1,800+ items, the quick list lost its usefulness
- Errors outside the F-pattern were often missed

### Action Bundle

The primary action adapts to machine state (a deployed machine doesn't offer "commission"), with bulk vs. inline actions made explicit.

![Before and after](/images/maas-application-layout/before-after.png)
![Action bundle](/images/maas-application-layout/action-bundle.png)
![Bundle proposal](/images/maas-application-layout/action-bundle-proposal.png)

An open question we validated: *how should actions display when machines in mixed states are selected?*

### Status Bar

Commissioning failures were detectable, but bottom-right placement sat outside the F-pattern.

![Commissioning fail](/images/maas-application-layout/commissioning-fail.png)

> "The bottom bug feels out of sight, very easy to not see.", David A., external user, 1,800 machines

### Quick Access List

Great for fast switching, until the list held 1,000 machines.

![Quick access list](/images/maas-application-layout/quick-access-list-2.png)

### Final prototype

<iframe style="border:none;max-width:100%;" width="800" height="450" src="https://embed.figma.com/proto/JRD38DvBBt6VMdqbW97vqO/Untitled?scaling=scale-down&content-scaling=fixed&page-id=0%3A1&node-id=1-4809&starting-point-node-id=1%3A9124&embed-host=share" allowfullscreen></iframe>

Test tasks covered the commissioning flow (how clear was the error?), comparing two machines in Quick Access, and which Status Bar information mattered most.

![Research summary](/images/maas-application-layout/research-summary.png)

{{< lens "judgment" >}}

- **Removed globals instead of adding them.** The IA fix meant nesting networking concepts properly and dropping under-used top-level items, while promoting logs and recurring tasks to first-class places.
- **Defaulted Quick Access to what matters, not everything.** Rather than list all machines, it defaults to **relevant states** (e.g., failed) and never reorders mid-action, a scope cut that made the feature survive at scale.
- **Took the Status Bar criticism seriously.** It tested "fine" on detection, but the bottom-right placement meant errors were missed. We logged it as a real failure and proposed a notification centre rather than shipping a pattern we knew leaked errors.
- **Named performance as the real ceiling.** No IA or list design helps when a page takes three minutes. Rather than design around slowness, we handed it to engineering, which became the [MAAS Search]({{< relref "/projects/maas-nlp-search" >}}) performance work.
- **Designed for the whole family, not just MAAS.** Choosing patterns that would generalise cost some MAAS-specific polish, and it's why they became the design system.

{{< lens "evidence" >}}

<div style="display:flex;flex-wrap:wrap;gap:2rem;align-items:flex-start;margin-bottom:2rem;">
  <div style="flex:1 1 300px;">

| Signal | Result |
| --- | --- |
| Design system | Layout **adopted into Canonical's design system** for Cloud & Infra apps |
| Cross-team | Other teams **pivoted to the new layout** after the final share-out |
| Action Bundle | Rated "very convenient" by **5 of 6** participants |
| Navigation | Errors decreased in testing with side nav + IA fixes (baseline: **9/10** misclicked) |
| Follow-on work | Surfaced the performance bottlenecks that became **MAAS Search** |
| Speed | **6 weeks**, 6 iterations, 6 participants |

  </div>
  <div style="flex:1 1 300px;">
    <img src="/images/maas-application-layout/timeline.png" alt="Project timeline" style="width:100%;height:auto;border-radius:6px;" />
  </div>
</div>

**What came next:** advanced search ([NLP Search]({{< relref "/projects/maas-nlp-search" >}})), a global and local notification centre, local error messages, and machine profile templating for storage and networking.

**What I learned:** performance drives UX value. IA and lists only help if the system is fast, and users who open five tabs are telling you exactly where it isn't.
