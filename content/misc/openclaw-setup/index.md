---
title: "From Infra Chaos to a Personal AI Operator: Building with OpenClaw"
date: 2026-04-07
draft: false
tags: ["AI", "Agents", "OpenClaw", "Anthropic", "Systems", "Infra"]
type: "Miscs"
---

## TL;DR

Built a personal AI operator using OpenClaw + Telegram + Claude.
Got stuck optimizing too early (local models, networking).
Reset everything.
Used OpenClaw onboarding + Anthropic API.
Connected Telegram.
System worked instantly.

> Biggest lesson: Getting something working > getting everything perfect.

---

## Introduction

Over the past few days, I set out to build something simple:

> a personal AI operator I could interact with through Telegram.

What I ended up building was not just a chatbot, but a small system:
- a remote agent
- backed by a model provider
- connected through a messaging interface
- with the potential to evolve into something far more autonomous

This post is not a tutorial. It's a reflection on what it actually takes to go from idea → working system, and what I learned along the way.

---

## The Goal

I wasn't trying to "use AI".

I wanted to answer a more interesting question:

> What does it mean to *own* an AI operator?

Not just prompt it, but:
- configure it
- route it through interfaces
- control its behavior
- eventually, delegate work to it

---

## Final Architecture

```
Telegram
   ↓
OpenClaw (VPS on DigitalOcean)
   ↓
Claude (Anthropic API)
```

At a glance, it’s simple. In practice, each layer introduces a different kind of complexity:

- **Interface layer (Telegram)** → async, user-facing interaction
- **Agent layer (OpenClaw)** → orchestration, routing, memory
- **Model layer (Anthropic)** → reasoning and generation

The interesting part isn’t any single layer — it’s what happens when they talk to each other.

---

## What Actually Worked

I went through a messy first attempt before landing on a clean path. Here’s what I’d do from scratch.

**Start fresh.** I wasted time trying to debug a broken state — leftover configs, half-installed tools, conflicting setups (Ollama, Tailscale). Resetting the droplet and starting clean was faster than untangling the mess.

> Lesson: don’t debug a messy state — reset.

**Use the DigitalOcean console, not SSH.** During setup, SSH key issues and connection latency added unnecessary friction. The browser console just works.

> Lesson: reduce friction during setup.

**Install Node, then OpenClaw:**

```bash
apt update && apt install -y curl git
curl -fsSL https://deb.nodesource.com/setup_24.x | bash -
apt install -y nodejs
npm install -g openclaw
```

**Run onboarding — don’t skip it:**

```bash
openclaw onboard --install-daemon
```

The onboarding wizard handles what would otherwise require manual config edits. I selected Express, chose Anthropic as the model provider, pasted my API key, enabled Telegram, and pasted the bot token. Done.

> Lesson: use onboarding — don’t hand-edit config.

**Pair Telegram.** The bot returns a pairing code:

```
Pairing code: XXXXX
```

Approve it on the VPS:

```bash
openclaw pairing approve telegram <CODE>
```

**The billing error I didn’t expect.** At some point I hit this:

```
API provider returned a billing error
```

The root cause surprised me: Claude Pro and Anthropic API credits are separate. Having an active Claude subscription doesn’t give you API access. I had to add credits in the Anthropic console, generate a new API key, and re-run onboarding.

> Lesson: always create a fresh API key after enabling billing. Old keys don’t pick up new credit balances reliably.

**Verify the system is running:**

```bash
openclaw gateway status | cat
```

You want to see:

```
Runtime: running
RPC probe: ok
```

---

## The First Working Moment

After all of it — the reset, the installs, the billing detour — I opened Telegram and sent:

```
Hello
```

![OpenClaw](/images/OpenClawSetup/Hello.png)

It responded. The system worked.

That moment is smaller than it sounds, and also larger than it sounds. It’s the difference between reading about agentic AI and having one running somewhere that is yours on your server, connected to your account, ready to be shaped into whatever you decide to build next.