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
hero = '/images/agent-warroom/incident-intake.svg'
cardHero = '/images/agent-warroom/war-room-overview.svg'
hook = 'When an AI skips validation, the human needs a war room, not another prompt to approve.'
description = 'A working three-agent prototype for diagnosing and remediating an infrastructure incident caused by another AI, designed around evidence, reversibility, and meaningful human control.'
repository = 'https://github.com/amylily1011/agent-warroom'
hideLensNav = true
+++

## Problems

At the Canonical AI Ubuntu Hackathon, I built **Agent War Room** around an uncomfortable but plausible operational failure: an AI deploy agent pushes an invalid nginx directive across a fleet and one VM fails to reload.

The immediate incident is technical. The larger problem is organisational. When an AI makes an infrastructure change, the on-call person inherits the consequence but often cannot see the decision, the evidence, or the validation that led to it.

### The operating problem

In the simulated incident, `deploy-agent` emits an invalid directive, `http2_max_concurrent_streams`, and pushes it to mocked Multipass VMs. The reload on `vm-web-01` fails. A normal alert tells an operator that something is broken. It does not tell them whether the configuration was reasoned about, checked, or merely guessed.

### The management and leadership problem

Adding approval prompts to every agent write is not a serious control model. It turns an accountable operator into a rubber stamp and makes recovery slower without increasing understanding. Asking a manager or on-call lead to verify every technical choice is equally unrealistic.

The leadership question became: **how might a team move quickly during an AI-caused incident while retaining clear accountability, sensible escalation, and the ability to stop the system?**

## AI opportunities

The opportunity was not to make three agents look busy. It was to give each one a bounded operational responsibility and make their work inspectable.

- **Optimist** looks for the fastest, lowest-cost recovery path.
- **Paranoid** gathers evidence, tests assumptions, and identifies risk.
- **Decider** acts as incident commander, reconciles the options, and writes the post-mortem.

This separation creates productive disagreement rather than a single opaque recommendation. Crucially, Paranoid can pull `deploy-agent`'s own reasoning trace. In the live scenario, that trace contains the line **`VALIDATE skipped`**. The AI has left the evidence of its missed validation step inside the incident.

![Agent War Room incident intake](/images/agent-warroom/incident-intake.svg)

## Experience

### My role

I designed and built the prototype end to end: the incident flow, operational roles, human-control model, Streamlit interface, and small Python handoff loop. I used the Claude Agent SDK and a deliberately lightweight orchestration loop of roughly 80 lines rather than a multi-agent framework.

### The incident flow

1. A reload-failure alert opens the war room.
2. Decider frames the incident and delegates the fast-path check to Optimist.
3. Paranoid gathers the service journal, `nginx -t`, config diff, commit history, fleet status, and the deploy agent's reasoning trace.
4. Decider presents a proposed recovery action with its evidence and risk.
5. A human chooses whether to approve, hold, or select a different action.
6. Decider records the decision and the post-mortem.

The experience makes the human's job explicit. They do not have to re-derive the diagnosis at incident speed. They are there to bring operational context, own a consequential decision, and stop the system when necessary.

![Agent War Room evidence trail](/images/agent-warroom/evidence-trace.svg)
*The deploy agent's own trace makes the missed validation step visible before a recovery decision is made.*

## Judgment

### Gate irreversibility, not every write

The key interaction decision was to place human review at an **irreversibility boundary**, or when the agents materially disagree. Reversible investigation and low-risk actions should not demand a click simply to simulate oversight.

The prototype renders a Slack-style approval card for the proposed config revert so the decision and audit trail are visible in the demo. The broader design principle is narrower: a human should be interrupted when accountability is needed, not whenever an agent mutates state.

![Agent War Room approval gate](/images/agent-warroom/approval-gate.svg)
*The approval card gives the accountable operator a meaningful choice: approve, redirect, or hold.*

### Bring lead indicators into the room

Fleet status is part of the decision, not background telemetry. In this scenario, the dashboard would have shown the bad push around 14 minutes before the reload alert. I brought capacity, unvalidated AI commits, and the last fleet-wide syntax check into the same surface as the recommendation.

For an incident lead, this changes the conversation from "can we fix the failing VM?" to "what did the system tell us before it became an outage, and what should change next?"

### Define human oversight honestly

The human is not the model's correctness reviewer. Their value is accountability, organisational context, and a kill switch. This is a management decision as much as an interface decision: it creates a clear owner for risk while allowing the agents to perform the specialised recovery work.

## Evidence

Agent War Room is a working prototype and demo, not a shipped product. Its evidence is therefore in the interaction model and the live scenario, not adoption or percentage metrics.

The prototype demonstrates that:

- the incident can move from alert to evidence to a human decision in one coherent surface
- operational roles can make disagreement legible instead of hiding it inside one answer
- the originating AI's trace can become first-class evidence when that AI caused the incident
- fleet signals can give an incident commander context before and during the failure

I ran the prototype through the nginx scenario and verified the real interface states for the alert, evidence trace, fleet-status degradation, and approval gate. The captured views show the deploy agent's skipped validation alongside the recovery decision.

What this prototype does **not** demonstrate is production adoption, reliability across real infrastructure, or measured incident outcomes. Those need validation with an operating team and real integration points.

### Capture plan

- Alert and fleet-status view at incident recognition
- Evidence trace showing the invalid directive and `VALIDATE skipped`
- Approval gate for the proposed config revert
- Final Decider handoff and post-mortem
