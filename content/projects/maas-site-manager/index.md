+++
title = 'Scaling Infra UX: A Centralized Control Platform for 60K MAAS Sites'
linkTitle = 'MAAS Site Manager'
date = 2022-03-12T17:15:51+01:00
draft = false
featured = true
weight = 6
year = '2022'
role = 'Lead UX Designer'
hero = '/images/work-cards/maas-site-manager.svg'
hook = '60,000 sites. One control plane. Image updates from hours per site to minutes.'
description = "Telco and enterprise operators were running thousands of MAAS instances one at a time, re-uploading the same custom images at every site. Six weeks of research with 7 organisations produced the MVP concept that informed Canonical's cluster-management direction."
aliases = ['/projects/projectdirectory/maassitemanager/', '/projects/projectDirectory/maasSiteManager/']
+++

**TL;DR:** MAAS was designed around one instance. Customers were planning for 60,000. I led the research and concept for a central control plane: what operators at that scale actually need to see, fix, and push, and just as importantly, what they don't want automated.

{{< lens "problem" >}}

MAAS is Canonical's bare-metal provisioning tool, used by enterprise and telco clients to manage thousands of machines. As deployments scaled toward **60,000+ edge sites**, operators hit the limits of managing each instance separately:

- **Fragmented management** across thousands of MAAS instances
- **Manual, redundant image handling** at every site
- **Delayed observability** and no single source of truth
- **Inconsistent UX** between sites

The ambiguity: nobody knew what "managing 60K sites" should look like, because the customers' setups had almost nothing in common. **How might we** let organisations monitor health in real time, push fixes globally, and control RBAC, networking and profiles from one place, without sacrificing scale, security, or existing workflows?

**My role:** Lead UX Designer with Product, Engineering, VPs, and Field Engineers. I directed two research rounds with 7 enterprise clients across telco, cloud, and retail, BT, VMware, AMD, Square, Home Depot, Tata, and Canonical Bootstack, then designed the dashboard, image management, and monitoring-integration concepts.

### Three very different archetypes

**1. Cloud regions on AWS.** Two clients ran region controllers on AWS RDS to avoid managing Postgres, each MAAS instance a Point of Presence for its data centres, some with 1,000+ machines per DC and ~10,000 per PoP.

![AWS as region controller](/images/maas-site-manager/amazon2.png)

Custom images had to be uploaded to the region, served to RDS, then synced back down to every rack, **~16 minutes locally, ~127 minutes to RDS**, then repeated for the next PoP. Manual, redundant, and expensive: the same data stored many times in cloud databases.

![Image sync latency](/images/maas-site-manager/amazon.png)

**2. Telco MEC edge.** Mini-sites at cell towers, 5–15 machines each, one MAAS instance per site, planned to grow to **~60,000 sites across the UK**, plus 25 core sites of 100–300 machines. Telcos have strict operating protocols and need any manager to plug into their NOC.

![Telco MEC](/images/maas-site-manager/MEC.png)

> If there's a power outage, it would be catastrophic if our phones don't work.

**3. Region + rack setups.** Fewer sites, multiple instances, a semiconductor lab linking 2 MAAS instances to 4 data centres, and a US retailer serving internal teams from 7 instances across 2 DCs.

![AMD](/images/maas-site-manager/AMD.png)
![Home Depot](/images/maas-site-manager/HomeDepot.png)

**Shared pain across all three:** redundant custom image uploads, no cross-site visibility or version consistency, no delegated RBAC, and slow troubleshooting across geographies.

{{< lens "ai" >}}

*Retrospective: this 2022 concept didn't include AI. Here's where I'd now see the lever, and where the research says to stop.*

At 60,000 sites, no human can read every status. Centralised health data and monitoring integration (the LMA pillar of the MVP) is exactly the substrate AI needs to **detect anomalies, cluster related failures, and rank what deserves attention first**.

But the strongest finding from this research sets the boundary: **operators didn't want more automation, they wanted control at a glance.** Telco NOCs run strict, auditable remediation protocols. The right role for AI here is triage and explanation that makes the at-a-glance view sharper, not autonomous action on production infrastructure.

{{< lens "experience" >}}

We plotted every need from the three archetypes on importance vs. urgency to decide what the MVP should be:

![Importance vs urgency](/images/maas-site-manager/importanceVSurgency.png)

### MVP concept

![Proposal](/images/maas-site-manager/proposal.png)

1. **Centralised image management**, one upload and distribution pipeline, deduplicated storage
2. **Multi-scale dashboard**, geolocation grouping for 60K+ sites, health at a glance
3. **Monitoring integration**, plug existing logging, monitoring, and alerting (LMA) stacks straight in
4. **Networking & profile management**, central subnet, routing, and profile configuration
5. **Seamless authentication**, trust-based MAAS-to-Site-Manager link, no repeated sign-ins

### First prototype

A lightweight prototype to test concepts with our primary group, not a final design.

**Dashboard**, the core problem: show 60K+ locations at once. Sites group by region, location, or organisational structure, with a clustered map and status indicators.

![Dashboard](/images/maas-site-manager/Dashboard.png)

**Listing view**, switch to a list and drill into any instance for machine-level management.

![Listing](/images/maas-site-manager/listingView.png)

**Initial settings**, automated tagging and filtering pulled from the MAAS API.

![Settings](/images/maas-site-manager/settings.png)

{{< lens "judgment" >}}

- **Designed for visibility and delegation, not automation.** It would have been easy to pitch an "auto-heal everything" platform. Research said the opposite, so the concept centres on control at a glance and plugging into the NOC tools operators already trust.
- **Ranked by importance × urgency, not by who asked loudest.** Image management won because it hurt in all three archetypes; region+rack setups taught us nothing new beyond configuration, so they didn't drive the MVP.
- **Integrated instead of rebuilt.** Telcos already had monitoring stacks with strict protocols. Plugging into them was more valuable, and more adoptable, than building our own.
- **Kept the prototype deliberately rough.** It existed to test concepts, not to sign off pixels. Progressive disclosure came out of that: too much detail overwhelmed users, too little left them unsure.
- **Narrowed before going deep.** Rather than build all five pillars, the next step was to pick **two concepts** and take them into detailed design and a simple test prototype, and to interview internal DC engineers before assuming they needed it too.

{{< lens "evidence" >}}

<div style="display:flex;flex-wrap:wrap;gap:2rem;align-items:flex-start;margin-bottom:2rem;">
  <div style="flex:1 1 300px;">

| Signal | Result |
| --- | --- |
| Research | **7 organisations**, 3 infrastructure archetypes, 2 research rounds |
| Speed | **6-week** research and prototype cycle |
| Product direction | Informed Canonical's future **cluster-management** product |
| Design system | Patterns adopted across the **Cloud & Infra design system** |
| Image management (concept) | From **hours per site** (~127 min per PoP to RDS) to **minutes, centrally** |
| Cost | Potential cloud storage savings by removing duplicate image copies |

  </div>
  <div style="flex:1 1 300px;">
    <img src="/images/maas-site-manager/timeline.png" alt="Project timeline" style="width:100%;height:auto;border-radius:6px;" />
  </div>
</div>

<!-- TODO: add what shipped from the concept and any adoption numbers from the cluster-management product. -->

**What I learned:** at scale, infrastructure management is about visibility and delegation. Designing for distributed systems means aligning technical constraints with cognitive load, and early prototypes can shape long-term product direction far beyond their fidelity.
