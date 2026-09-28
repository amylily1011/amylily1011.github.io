+++
title = 'CLUX: A Roast-Proof Scorecard for Your Sad Little CLI'
linkTitle = 'CLUX'
date = 2026-07-16T16:41:00+01:00
draft = false
featured = true
weight = 2
year = '2026'
role = 'Designer & builder · solo'
hero = '/images/work-cards/clux.svg'
hook = '88% of confused users never file an issue. CLUX notices for them.'
description = "The terminal has no heatmaps or session recordings, so bad CLI design festers until users quietly uninstall. CLUX uses an LLM to score any CLI's help and output against a 46-signal usability rubric, for humans and for AI agents, and returns ranked, before/after fixes."
aliases = ['/misc/clux/']
+++

**TL;DR:** After six years designing, auditing, and writing guidelines for command-line tools, I turned that judgment into a tool. Paste a CLI's `--help` or output; get a usability score with an honest uncertainty range, and fixes ranked by severity. The model reads the evidence; deterministic code does the scoring.

→ **[Try the live prototype](https://clux-eta.vercel.app/)** · **[View on GitHub](https://github.com/amylily1011/clux)**

{{< lens "problem" >}}

The question I get most is *"What makes a good CLI?"* The honest answer is: **it depends on who, or what, is running the command.** A confirmation prompt is thoughtful safety for a human hovering over delete, and a pipeline-killer for a script running unattended at 3am.

That's the first hard part. The second is worse: **CLI design quality is invisible until users complain.** GUIs get analytics, heatmaps, and session recordings. The terminal gets nothing. Roughly **88% of users won't file an issue** when a tool confuses them, they just `brew uninstall` and move on.

So teams ship CLIs with no feedback loop, and "ease of use" gets argued on vibes. I wanted it measurable. The criteria I use:

| Criteria | What it asks |
| --- | --- |
| **Learnability** | Can you find commands without reading the source? Does `--help` help? |
| **Efficiency** | Shortcuts, pipe-friendliness, `--json`, or three extra flags and a prayer? |
| **Error tolerance** | When it breaks, does it say *why* and *what to do next*? |
| **Pleasantness** | Consistent naming and output, or a different committee per subcommand? |
| **Safety** | `--dry-run`? Confirmation before destruction? |
| **Security** | Do credentials land in a config with sane permissions, or your shell history? |
| **Accessibility** | Does it work with `--no-color` and screen readers? |
| **UNIX compliance** | Does it compose, stdout vs. stderr, exit codes, pipes? |

Every one of these fights the others depending on the audience. That tension is the problem CLUX has to model, not flatten.

{{< lens "ai" >}}

**Why an LLM:** a usability expert reviewing a CLI is mostly *reading*, help text, error messages, output formats, and judging them against heuristics. That's a language task that normally needs a person and a user study. A model can do the reading **at scale, on demand, without recruiting a single participant**, which is exactly what a feedback-starved medium needs.

**Why not *only* an LLM:** a model asked "score this CLI out of 100" will happily produce a confident, unrepeatable number. So I split the job:

- **The model** rates **46 specific signals** from 0–4, and must quote the evidence behind each rating.
- **Deterministic code** turns those ratings into the score. The scoring weights live on the server and are **never in the prompt**, so the model can't game or drift them.
- **Missing evidence is an unknown, not a pass.** Unknowns widen the score's range instead of silently inflating it.

The model does what models are good at, reading and judging language. Everything that has to be consistent stays in code.

{{< lens "experience" >}}

**1. Paste evidence.** A command's name, `--help`, output, or error text.

**2. Pick the audience.** *Human-first* or *AI-agent first*. The same rubric is reweighted: agents care far more about safety, predictability, and machine-readable output than about pleasant colours.

**3. Optionally bring rules.** None, the UNIX philosophy, or **your organisation's own style guide**, the fifteen-page doc everyone cites once in onboarding and never opens again. CLUX checks every CLI against it, every time, without getting tired or petty.

![CLUX org rules](/images/clux/OrgRules.png)

**4. Read the report.** A score out of 100 **with its range**, a grade, how many signals could be checked, a radar across dimensions, and **current behaviour → recommended fix** pairs ranked by severity, with before/after terminal snippets.

![CLUX demo](/images/clux/clux_demo.gif)
![CLUX summary output](/images/clux/summary.gif)

**5. Stay sceptical.** A **confidence score** sits on every finding, on purpose, and a little annoyingly.

![CLUX confidence score](/images/clux/confidence.png)

A public **/methodology** page renders the full rubric, with a worked example computed by the real scoring code.

**Tools & tech:** Next.js, Vercel AI SDK (Anthropic, Google, or Kimi models), Supabase, Recharts, shadcn/ui.

{{< lens "judgment" >}}

- **No One True Score.** Rather than one universal standard, CLUX weights the rubric by audience, because holding a `curl | bash` pipeline to the manners of a first-time user is wrong in both directions.
- **Killed "scripting" as a third audience.** Early versions offered Human-first, Scripting-first, and Agent-first. But humans and agents *both* script, so scripting isn't an audience, it's a capability. It now appears as a comparison in every report, next to the audience score. The two can disagree, and the report shows both.
- **Refused to let a score look perfect on thin evidence.** Unknowns count as a midpoint with a visible range; a dimension with fewer than 2 rated signals shows **–** instead of a number; one confident critical finding caps the grade at D, however good the rest looks.
- **Kept rules from moving the score.** Custom-rule violations appear in the same ranked fix list, which half of the system noticed a problem is no reason to rank it lower, but they never change the usability score or grade cap. Your house style isn't universal usability.
- **Designed friction on purpose.** The confidence score exists to interrupt the habit of nodding along with AI output. The machine can flag the smell; you still decide whether it's worth fixing.
- **Cut the design-level rules from single-command reports.** Whether a CLI divides purposes well across commands ("1 command ↔ 1 purpose") needs the whole repository. Judging it from one pasted command would be guessing, so it doesn't.

{{< lens "evidence" >}}

| Signal | Result |
| --- | --- |
| Shipped | **Live prototype** on Vercel, open source on GitHub |
| Rubric | **46 signals** across 8 dimensions, published in full on the methodology page |
| Benchmarks | Benchmark fixtures from real CLIs: `git status`, `docker ps`, `gh pr create`, `tar`, `multipass find` |
| Reliability | Scoring constants are tested against the rubric file, a test fails if code and rubric drift apart |
| Accessibility of the tool itself | Runs without an API key in **demo mode**, so anyone can see the report before plugging in a model |

<!-- TODO: add usage numbers (evaluations run, GitHub stars, teams using custom rule sets) and any qualitative feedback. -->

**What building it taught me:** the design work in an AI product is mostly deciding **what the model is *not* allowed to decide.** CLUX became trustworthy the moment scoring moved out of the prompt.
