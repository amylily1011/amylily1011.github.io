+++
title = 'Ubuntu Pro CLI: Redesigning `pro status` for Clarity, Scale, and Actionability'
linkTitle = 'Ubuntu Pro CLI'
date = 2024-04-12T17:15:51+01:00
draft = false
featured = true
weight = 4
year = '2024'
role = 'Lead UX Designer'
hero = '/images/work-cards/ubuntu-pro-cli.svg'
hook = 'One overloaded command became four clear ones. Support tickets dropped ~45%.'
description = "`pro status` was the front door to Ubuntu Pro security and compliance, and it had grown into an inconsistent wall of text operators couldn't act on. I split it into four single-purpose commands with state-aware next steps, cutting related support tickets by ~45% in pilot."
aliases = ['/projects/projectdirectory/ubuntuproclient/', '/projects/projectDirectory/ubuntuProClient/']
+++

**TL;DR:** The CLI that told enterprise operators whether their machines were secure had become hard to read and harder to act on. I redesigned the command structure and every output state around "one command, one purpose", kept every old command working, and shipped a spec engineering could build against.

{{< lens "problem" >}}

Ubuntu Pro provides security, compliance, and extended maintenance for enterprise and developer machines. `pro status` is the **primary entry point** for operators to check subscription, security coverage, vulnerabilities, and service health.

As the portfolio grew across **LTS, EOSS, non-LTS, and cloud-attached systems**, the output became **inconsistent, hard to parse, and hard to act on**, especially for operators managing dozens or hundreds of machines.

![pro status before](/images/pro-status/Old-jammy.png)
*Before: one command carrying subscription, services, and security in fragmented, verbose text.*

The ambiguity was in the edges. The same command had to behave well for:
- attached, unattached, and expired machines (with and without grace periods)
- end-of-support releases that needed an upgrade prompt
- cloud auto-attach failures
- humans in a colour terminal **and** scripts reading non-TTY output

**How might we** unify every `pro status` output into one consistent, human-readable format that guides people to the next action, without breaking the automation already built on top of it?

**My role:** Lead UX Designer across the Server, Compliance, and Support teams. I audited existing outputs, wrote the interaction and layout spec for every main and edge case, defined colour/icon/text conventions that adapt to terminal themes, and worked with engineering on backward compatibility.

{{< lens "ai" >}}

*Retrospective: AI is not part of this product, and that's deliberate territory.*

Security and compliance status is a place where output must be **deterministic and exact**: an operator acting on "3 fixable CVEs" needs that number to be true every time, not a plausible summary. The right lever here was structure, not generation.

What the work *did* set up is the ground AI agents need. Consistent sections, stable wording, clean non-TTY output, and **exact next-step commands** are what make a CLI legible to an agent working through text alone, not just to a person. Designing `pro` taught me that CLI quality for machines and CLI quality for humans can be specified, and measured, which is the idea that later became [Clux]({{< relref "/projects/clux" >}}).

{{< lens "experience" >}}

### Design principles

1. **Consistency first**, tables, sections, and labels keep the same order and wording.
2. **Signal over noise**, security and subscription surface first.
3. **Progressive disclosure**, summaries in `pro status`, details in `pro service show`.
4. **Accessibility & compatibility**, colour-free mode, UTF-8 detection, non-TTY handling.
5. **Context-aware guidance**, suggestions adapt to the machine's state.

### One command became four

`pro status`, `pro security status`, and two new commands: `pro service` and `pro subscription`. Commands were regrouped into Quick Start, Service, Security, and Troubleshooting, using a consistent **verb-noun** pattern (`pro service enable`, `pro service show`).

**`pro status`**, more concise, skimmable, with important messages highlighted.

![pro status](/images/pro-status/pro-status.png)
![pro status before and after](/images/pro-status/before-after-pro-status2.png)

**`pro security status`**, the command users found most confusing. Now a glanceable view of fixable CVEs and vulnerabilities, ordered by severity, with package coverage broken down by source and the exact command to fix.

![pro security status](/images/pro-status/pro-security.png)
![security status before and after](/images/pro-status/before-after-security-status.png)

**`pro service` (new)**, lists services and what each entitles, then lets you drill into one with `pro service show [name]`.

![pro service](/images/pro-status/pro-services.png)
![pro service show](/images/pro-status/pro-service-show.png)

**`pro subscription` (new)**, sysadmins asked for this to maintain infrastructure and know who to contact for support. It used to be buried inside `pro status`.

![pro subscription](/images/pro-status/pro-subscription.png)

Designed states include the warning before expiry and the expired state:

![Subscription almost expired](/images/pro-status/pro-subscription-almost-expired.png)
![Subscription expired](/images/pro-status/pro-sub-expired.png)

### Edge cases and accessibility
- `--no-color` and the `NO_COLOR` environment variable
- UTF-8 detection with fallback characters
- **Clean non-TTY output** for automation
- `--no-suggestions` for experienced operators

{{< lens "judgment" >}}

- **Split the command instead of polishing it.** The easy path was reformatting `pro status`. I pushed for "one command, one purpose": subscription details didn't belong in a health check, so they got their own command and became easier to find.
- **Kept every old command working.** A clean break would have been simpler to design, but operators had scripts on top of this output. New structure shipped alongside preserved commands, compatibility cost spec complexity, and it was worth paying.
- **Summaries up front, detail on request.** Progressive disclosure was a tradeoff against completeness: `pro status` shows less than before, deliberately, and points to the command that shows more.
- **Balanced first-timers against power users.** Contextual suggestions help newcomers; `--no-suggestions` and clean non-TTY output keep the tool quiet for experts and scripts.
- **Made the spec extensible, not just correct.** Service templates (FIPS, esm-apps, esm-infra, Livepatch) mean new services plug in without re-litigating layout.

{{< lens "evidence" >}}

| Signal | Result |
| --- | --- |
| Support tickets related to `pro status` | **~45% reduction** in pilot |
| Operator time-to-action | Faster, via precise, state-specific next-step commands |
| Engineering | A **single-source CLI spec** covering every main and edge state |
| Extensibility | New services plug into the same template without breaking consistency |

| Before | After | Impact |
| --- | --- | --- |
| Inconsistent service lists | Standard table: name, status, entitlement, description | Less confusion |
| No clear next steps | Contextual suggestions with exact commands | Better task completion |
| Hard-coded colours | Theme-aware colours with no-colour fallback | Accessibility |
| Subscription buried in text | Dedicated `pro subscription` command | Fewer missed critical details |

**What I took away:** CLI UX *is* product UX. Consistency, accessibility, and context matter just as much in a terminal, and backward compatibility is a design constraint, not an engineering footnote.
