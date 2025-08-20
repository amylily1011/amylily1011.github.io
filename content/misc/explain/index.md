+++
title = 'From Confusion to Clarity: Designing `/explain` to Make Concepts Click'
date = 2022-03-12T17:15:51+01:00
draft = false
type = "misc"
+++

**Context:**  

At Canonical, internal teams often work in highly specialized domains — cloud infrastructure, AI, enterprise support, etc. As a product designer, I frequently saw confusion arise when teams shared updates full of acronyms, assumptions, and product-specific jargon.

---

### Why?

During a design review, I realized that while I thought I had clearly explained the mental model behind LXD integration in MAAS, my colleagues didn’t follow.

![explain lxd in MAAS](/images/explain/maas-explain.png)

Little did I know, this was how other designers understood my explanation. 

![NapImust](https://i.imgur.com/lBBxrh1.jpeg)
*Image source:* [imgur.com/lBBxrh1](https://imgur.com/lBBxrh1)


In that very same meeting, below was how my colleague explained the concept of Ubuntu Advantage.

![Ubuntu Advantage](/images/explain/will-explain.png)

Yet, this was how I understood it.

![Need help](https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExYnp4ZXI3N2s3a3llZGV2YnRnbTAyZWc0c2p1c3MwNTR3ZDNrN3M5byZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/oGzFZek2lszlK/giphy.gif)
*GIF via* [GIPHY](https://giphy.com/gifs/help-big-bang-theory-me-oGzFZek2lszlK)

---

## Problem?

**We work with a lot of complicated concepts:**

We had no quick, lightweight way to understand technical concepts outside of our own teams.

Here are some examples of terms that often confuse people outside their teams:

| **Concepts** |
| --- | 
| SmartNICs|
| TFA |
| Multi-access Edge Computing (MEC) |
| Ceph |
| Canadisk? |

**Without shared language,** even well-designed features risked being misaligned or miscommunicated.

## Approach

I created `/explain` — a slash command integrated into Mattermost (our internal chat platform). 

Anyone could type:

```
/explain [term]
```

![Explain command](/images/explain/explain-command.gif)

It returns a simple, friendly, human-readable explanation of a concept — often sourced from PMs, engineers, or documentation. Think “just enough context” without the Wikipedia rabbit hole.

**To build momentum:**
- I seeded the database with common terms people struggle with.
- Crowdsourced the definition from internal people who are team experts or Technical Authors.

![Explain data](/images/explain/data-base.png)


When the `[term]` does not exist, someone with the knowledge can contribute to the meaning of that term.

![Explain something](/images/explain/explain-something.png)


## Pro tip

If you have a **bad day** and you don't want to learn new concepts, you can type `/explain why`. This will return jokes from the Unix fortune database [bsd-fortune](https://en.wikipedia.org/wiki/Fortune_(Unix)). 

(Fortune database from HubTou)[![GitHub Repo](https://img.shields.io/badge/view-on%20github-blue?logo=github)](https://github.com/HubTou/fortunes-historical/tree/main)

![explain why](/images/explain/explain-why.gif)


---
## Outcome

> Sometimes the best UX isn't a screen...it's helping people *understand* what's on it.

- `/explain` was adopted organically by multiple teams.
- PMs and Engineers contributed to definitions, turning it into a share glossary.
- I helped onboard new hires, spark discussion, and normalize asking *What does that mean?*

This became more than just a tool, it created a space for *learning out loud* without slowing anyone down. 

---
## Reflection

This side project reminded me that UX isn't always about visual, sometimes it's a *cultural design intervention.*

By turning misunderstanding into a moment of play and clarity, `/explain` showed how a small, targeted tool can shift team dynamics, unblock ambiguity, and scale knowledge in distributed environments. 

> Maybe *good design* isn't always about polishing. Sometimes, it's about lowering the cost of *asking a "dumb" question*.

![explain why](/images/explain/the-end.png)
