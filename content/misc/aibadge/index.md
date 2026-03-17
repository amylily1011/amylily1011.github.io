---
title: "AIBadge: A Weekend Experiment about AI Provenance"
date: 2026-02-09
draft: false
tags: ["AI", "UX", "Experiment", "Security", "Provenance", "Side Project"]
categories: ["UX", "AI"]
summary: "summary: A meeting question about AI disclosure led to a weekend experiment exploring trust, authorship, and verifiable integrity in the age of generative AI."
---

## The question that started it

In a management meeting recently, a colleague asked a simple question:

> *“Should we add a badge to indicate that AI was used to write this?”*

What followed wasn’t simple "yes" or "no".

Some people felt transparency was important. Others worried that admitting AI use might undermine credibility, create unnecessary scrutiny, or send the wrong signal from a company perspective.

The conversation quickly became less about design and more about culture, perception, and trust.

That question stayed with me after the meeting.

Not because I thought a badge was the right solution — but because it revealed a deeper tension:

**In a world where AI is everywhere, what does honesty about authorship actually look like?**

---

## Isn't this just another AI detector?

I didn't want to build another AI detector. 

Most AI detectors can provide a probability that this is written by AI and often produce high rates of false positives. 

- You can edit AI text to sound human
- You can rewrite human text to sound like AI
- Models change constantly
- AI detection is somewhat guessing

So instead of asking:

> *“Can we detect AI?”*

What if the question is:

> *“What if authors simply tell the truth and stand behind it?”*

---

## The idea: signed content

In many domains, we don’t try to detect behavior.  
We rely on **signed declarations**.

- Authors sign their work  
- Journalists attach bylines  
- Developers sign software releases  
- Organizations publish statements under their name  

These systems work because of **accountability**, not surveillance.

Could content work the same way?

Instead of guessing how something was written:

**The author declares how it was created then cryptographically signs that claim.**

This idea became a small weekend experiment.

---

## AIBadge: a proof-of-integrity experiment

Let's call this simple prototype **AIBadge**. Since I couldn't think of a better name yet.

The idea:

1. The author chooses a disclosure:
   - Human-written  
   - AI-assisted  
   - AI-generated  

![select](/images/aibadge/select-sign.gif)

2. The system creates a cryptographic signature of:
   - The content (via a hash)  
   - The disclosure label  
   - Metadata (time, model, issuer)

![hashSignature](/images/aibadge/signature.png)

3. A token can be shared alongside the content.

Anyone can verify:
- The claim was signed  
- The content has not been modified since it was signed  

![verify](/images/aibadge/verify.gif)

If even one character changes, verification fails.
![mismatch](/images/aibadge/mismatch.gif)

This isn’t AI detection.

It’s **content integrity + declared provenance**.

---

## Adding the Chrome extension to this Prototype

I used Pastebin as a playground, but imagine writing an article on google docs or your blog post. 

As an author who wants to declare your content's integrity, you upload the written content on the blog with a cryptographic signature.

![chrome](/images/aibadge/chrome.gif)

The Chrome extension checks the signature and the text to verifies that **the content matches** the token. 

Otherwise, it will flag as **content mismatch** when there is a modification to the text.
![chrome-error](/images/aibadge/mismatch-chrome.png)


---

## What this proves (and what it doesn’t)

The system does **not** prove that content is truly human.

It proves that:

> The author is willing to stand behind a claim about this exact content.

This is closer to:
- A signed statement  
- A byline with accountability  
- A tamper-evident seal  

Trust still comes from identity and reputation.  
The signature simply ensures the claim cannot be altered. This is accountability. 

---

## Does this matter?

The original meeting question was about a badge.

But the real design question is:

**How do we design for trust when the boundary between human and AI is blurry?**

We are moving into a world where anyone can generate anything. When content can be rewritten endlessly. And when authenticity becomes harder to judge. 

Perhaps the future of trust should also include:
**explicit claims and verifiable integrity**

Instead of:
- “We will try to catch AI use”

Perhaps we are moving toward:
- “Authors choose what to disclose — and stand behind it”


This shifts the experience from **surveillance to responsibility**.

---

## The Reverse CAPTCHA idea

Traditional CAPTCHAs ask:

*Prove you are human.*

This experiment explores a flip of the model where:

**If you make a claim about your content, prove the claim hasn’t been altered.**

Not proof of humanity, but
Proof of integrity.

---

## What I learned

The technical part is actually simple, but the interesting part is the interaction design:

During the meeting discussion, I realized that:
- Transparency is cultural as much as technical  
- Trust signals must balance honesty and reputation risk  
- The future of AI UX in security may be about **provenance**, not just detection  
- The most interesting problems sit at the intersection of technology, incentives, and human behavior  

The meeting debate made sense.

People aren’t necessarily resisting transparency.

They’re navigating a new social contract around AI.

I guess what comes to mind, while putting this together was:

- How might we make provenance visible without adding friction?
- How might we signal trust without creating a stigma around AI?
- How might we make verification feel lightweight and contextual?
---

## What’s next

This AIBadge was just a tiny exploration of a bigger idea.

> What would the future look like for content authorship and integrity. Could provenance be part of this equation?

A future where:
- Content carries verifiable provenance  
- AI use is disclosed intentionally, not inferred  
- Trust comes from accountability, not guessing  

Because in the age of generative AI, the real question isn’t just whether:

*“Was this written by AI?”*

Maybe, it’s:

**Can I trust the origin of this content?**