+++
title = 'AIBadge: A Weekend Experiment in AI Provenance'
linkTitle = 'AIBadge'
date = 2026-02-09
draft = false
featured = true
weight = 5
year = '2026'
role = 'Solo · weekend experiment'
hero = '/images/work-cards/aibadge.svg'
hook = 'Stop guessing whether AI wrote it. Make the author sign for it.'
description = "A management debate about labelling AI-written content exposed a trust problem that AI detectors can't solve. AIBadge is a weekend prototype that lets authors declare how content was made and cryptographically sign that claim, so a single changed character breaks verification."
tags = ['AI', 'UX', 'Experiment', 'Security', 'Provenance', 'Side Project']
categories = ['UX', 'AI']
aliases = ['/misc/aibadge/']
+++

**TL;DR:** The question in the room was *"Should we add a badge when AI wrote this?"* The real question was *"How do we design for trust when the line between human and AI is blurry?"* I skipped the AI detector and built a signed declaration instead: authors state how content was made and stand behind it, tamper-evidently.

{{< lens "problem" >}}

In a management meeting, a colleague asked:

> *"Should we add a badge to indicate that AI was used to write this?"*

There was no simple yes or no. Some felt transparency mattered. Others worried that admitting AI use would undermine credibility, invite scrutiny, or send the wrong signal about the company. The conversation stopped being about a badge and became about **culture, perception, and trust.**

That's what made it hard: the problem was only partly technical. A badge anyone can add or remove proves nothing. A badge people are afraid to add is worse. **In a world where AI is everywhere, what does honesty about authorship actually look like?**

{{< lens "ai" >}}

The obvious "AI solution" was the wrong one. **AI detectors guess.** They output a probability, produce high false-positive rates, and are easy to defeat:

- Edit AI text until it sounds human
- Rewrite human text until it sounds like AI
- Wait, models change constantly

So instead of *"Can we detect AI?"* I asked: **"What if authors simply tell the truth and stand behind it?"**

We already trust content that way elsewhere, through **signed declarations**, not surveillance. Authors sign their work, journalists carry bylines, developers sign releases. Those systems work because of accountability.

The opportunity AI creates here isn't another model, it's a new **disclosure moment**. Once AI is part of how nearly everything is written, provenance becomes a UX problem: how to make an honest claim cheap to make and easy to check.

{{< lens "experience" >}}

**1. Declare.** The author chooses a disclosure: *Human-written*, *AI-assisted*, or *AI-generated*.

![Select a disclosure and sign](/images/aibadge/select-sign.gif)

**2. Sign.** AIBadge creates a cryptographic signature over the content's hash, the disclosure label, and metadata (time, model, issuer).

![Hash and signature](/images/aibadge/signature.png)

**3. Share.** A token travels alongside the content.

**4. Verify.** Anyone can check that the claim was signed and the content hasn't changed since.

![Verify](/images/aibadge/verify.gif)

Change a single character and verification fails:

![Mismatch](/images/aibadge/mismatch.gif)

**5. Verify in place.** A Chrome extension checks the token against the page text wherever you're reading, here using Pastebin as a stand-in for a blog or shared doc.

![Chrome extension](/images/aibadge/chrome.gif)

If the text was modified after signing, it flags a **content mismatch**:

![Chrome mismatch](/images/aibadge/mismatch-chrome.png)

{{< lens "judgment" >}}

- **Killed the detector before building it.** Detection is surveillance with a false-positive rate. It would have answered the literal question and failed the real one.
- **Claimed only what the system can prove.** AIBadge does **not** prove content is human. It proves the author stands behind a specific claim about this exact content, closer to a byline or a tamper-evident seal. Trust still comes from identity and reputation; I didn't dress it up as more.
- **Made disclosure a choice, not an accusation.** Three neutral labels, chosen by the author, shift the experience from *surveillance* to *responsibility*, and avoid turning "AI-assisted" into a stigma.
- **Flipped the CAPTCHA.** Instead of "prove you're human", the model is "if you make a claim, prove it hasn't been altered." Proof of integrity, not proof of humanity.
- **Kept it a weekend.** The cryptography is the easy part. I stopped at a working end-to-end loop, because the open questions are about interaction and incentives, not more code.

{{< lens "evidence" >}}

| Signal | Result |
| --- | --- |
| Shipped | Working end-to-end prototype: declare → sign → share → verify, plus a Chrome extension |
| Integrity | A **one-character edit** reliably fails verification |
| Reframe | Turned a yes/no badge debate into a design question about provenance and accountability |

<!-- TODO: add any reactions from the original meeting or from people who tried the prototype. -->

What the meeting taught me: people weren't resisting transparency. **They were navigating a new social contract around AI.** The questions I'd take forward:

- How might we make provenance visible without adding friction?
- How might we signal trust without creating a stigma around AI?
- How might we make verification feel lightweight and contextual?

Because in the age of generative AI, the question isn't only *"Was this written by AI?"*, it's **"Can I trust the origin of this content?"**
