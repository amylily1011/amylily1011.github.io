+++
title = 'Agent War Room: Designing Human Accountability for AI Incident Response'
linkTitle = 'Agent War Room'
date = 2026-09-28
draft = false
section = 'projects'
author = 'Amy Lily'
featured = true
weight = 1
year = '2026'
role = 'Product designer & builder · Canonical AI Ubuntu Hackathon'
homeRole = 'AI Ubuntu Hackathon'
hero = '/images/agent-warroom/war-room-overview.svg'
hook = 'When an AI skips validation, the human needs a war room, not another prompt to approve.'
description = 'A working three-agent prototype for diagnosing and remediating an infrastructure incident caused by another AI, designed around evidence, reversibility, and meaningful human control.'
repository = 'https://github.com/amylily1011/agent-warroom'
hideLensNav = true
+++

## Context

At the Canonical AI Ubuntu Hackathon, I built **Agent War Room**, a working prototype for a specific, uncomfortable scenario: an AI deploy agent pushes a bad configuration across a fleet, and another set of agents has to diagnose and remediate the incident.

The demo uses mocked Multipass VMs for reliability. A deploy agent pushes an invalid nginx directive; one VM fails to reload. The important detail is not only that a change failed. The Paranoid agent can pull the deploy agent's own reasoning trace and find the line that matters: **“VALIDATE skipped.”** The AI has left the evidence of its skipped validation step inside the incident itself.

![Agent War Room overview](/images/agent-warroom/war-room-overview.svg)

## My Role

I designed and built the prototype end to end: the incident flow, the three operational agent roles, the handoff loop, the Streamlit interface, and the human-control model. I used the Claude Agent SDK and Python, with a deliberately small handoff loop of roughly 80 lines rather than an orchestration framework.

## Challenge

The challenge was not to make three agents look busy. It was to make an AI-led incident response legible and accountable when another AI had caused the failure.

A generic approval prompt is not enough. If a person is asked to approve every write, they become a rubber stamp and a bottleneck. If they are asked to review every technical detail, the system assumes they can out-reason the agents under incident pressure. Neither model gives the human a useful job.

**How might we** design an agentic incident response that moves quickly, surfaces evidence, and keeps a human meaningfully in control without turning them into a permanent correctness reviewer?

## Design Principles

1. **Give each agent a real operational role.** Optimist looks for fast, low-cost recovery. Paranoid gathers evidence and tests risk. Decider acts as incident commander and writes the post-mortem.
2. **Make the evidence inspectable.** The system should show why an agent reached a recommendation, including the originating deploy agent's reasoning trace when it is relevant to the incident.
3. **Gate irreversibility, not every write.** Human approval should concentrate at the point where a change becomes difficult to undo.
4. **Bring lead indicators into the room.** A fleet-status signal that precedes an alert is useful incident evidence, not background telemetry.
5. **Define the human role honestly.** The human provides accountability, operating context, and a kill switch. They are not there to verify every line of agent reasoning.

## Scope of Work

The prototype covers one contained incident from detection through post-mortem:

- a deploy agent pushes an invalid nginx directive across mocked Multipass VMs
- one nginx reload fails
- Optimist proposes a quick, low-cost recovery path
- Paranoid gathers the failing configuration, service evidence, fleet status, and the deploy agent's reasoning trace
- Decider weighs the options, records the decision, and produces the post-mortem
- a human approval gate appears only when the proposed action crosses an irreversibility boundary

The interface is a Streamlit war room. The infrastructure is mocked so the demo remains deterministic and the design discussion can stay focused on the incident model.

## Before/After or Key Decisions

### Before: approval as a substitute for control

The tempting interaction is an approval dialog for every agent write. It creates an appearance of human oversight, but asks a person to repeatedly confirm actions they cannot realistically assess at incident speed. The result is delay without meaningful accountability.

### After: approval at the irreversibility boundary

I designed the gate around **irreversibility**. Reversible investigation and low-risk recovery can proceed within the agents' operational roles. When a proposed action has a lasting or difficult-to-reverse consequence, the system stops and asks the human to make the accountable decision.

![Agent War Room approval gate](/images/agent-warroom/screenshot-placeholder.svg)

### Lead indicators belong in the war room

The fleet-status dashboard would have shown the bad push around 14 minutes before the alert fired. That makes it an incident signal, not a secondary operations view. I brought this context into the same place as the evidence and options, so the Decider can reason from the warning signs as well as the failure.

![Agent War Room fleet status](/images/agent-warroom/screenshot-placeholder.svg)

### The human is accountable, contextual, and able to stop the system

The human's value is not perfect technical correctness review. They provide organisational context the agents do not have, own the decision when risk crosses a boundary, and can stop the system. This made the control model more concrete than “human in the loop.”

## Impact & Outcomes

Agent War Room is a working prototype and demo, not a shipped product. Its outcome is a design proposition rather than an adoption metric: an incident response can use multiple agents without hiding the source of a recommendation or making human oversight performative.

The prototype makes three choices visible in one flow:

- role-based disagreement can improve an incident decision when each role has a distinct mandate
- an agent's own trace can be first-class evidence when that agent caused the incident
- approval can be reserved for decisions that need human accountability, rather than added to every step by default

## Reflection & Learnings

The most useful reframe was that human oversight is not a binary switch. “Human in the loop” can still mean a person clicks through opaque prompts. In this prototype, the human is designed as the accountable operator with context and a kill switch, while the agents do the specialised work of recovery, risk analysis, and incident command.

I also learned that observability is part of the interaction design. The skipped validation step only becomes useful when it is connected to the failed reload, the fleet signal, and the recommended action. A trace is not trust by itself; it needs to be presented as evidence in the decision.

## Deep Dive

The screenshots below are intentional placeholders for the next iteration of the case study.

![Agent War Room agent roles](/images/agent-warroom/screenshot-placeholder.svg)
*Capture the Optimist, Paranoid, and Decider panels together, with each role's mandate visible.*

![Agent War Room evidence trail](/images/agent-warroom/screenshot-placeholder.svg)
*Capture the failed nginx reload beside the deploy agent's reasoning trace, including “VALIDATE skipped.”*

![Agent War Room decision and post-mortem](/images/agent-warroom/screenshot-placeholder.svg)
*Capture the Decider's recommendation, the irreversibility-based approval gate, and the generated post-mortem.*

**Shots to capture**

- The full war-room view at the point the incident is recognised
- The fleet-status lead indicator and the alert timeline
- The evidence drawer showing the deploy agent's skipped validation step
- The irreversible-action approval gate
- The final Decider handoff and generated post-mortem
