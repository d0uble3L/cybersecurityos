---
title: "When the Agent Is the Incident: NIST, NSA, and CISA's 10 Key Practices for SI Incident Response"
date: 2026-10-25
draft: false
author: "Michael Tayo"
tags:
  [
    "NIST CAISSI",
    "CISA",
    "NSA",
    "AI Incident Response",
    "Autonomous AI Agents",
    "Super Intelligence",
    "AI Security",
    "NIST SP 800-61r3",
    "NIST CSF 2.0",
  ]
categories: ["AI and Security", "Security Operations"]
description: "NIST CAISSI, NSA SISC, and CISA published 10 voluntary key practices for incidents during super intelligence training and evaluation. The new idea: the incident is the agent itself. Here's what changes for your IR playbook."
slug: "nist-cisa-nsa-si-incident-response-key-practices"
show_reading_time: true
featured_image: /posts/ai-devsecops/images/si-incident-response-social.png
images:
  - /posts/ai-devsecops/images/si-incident-response-social.png
keywords: "NIST CAISSI key practices, SI control incident, super intelligence incident response, AI agent incident response, NSA SISC, CISA AI guidance, NIST SP 800-61r3 AI agents, agentic AI containment, AI agent monitoring, training-safe recovery, SI control near-miss, AI agent shutdown policy, frontier AI incident reporting"
faq:
  - q: "What did NIST, NSA, and CISA publish in October 2026?"
    a: "On October 16, 2026, NIST's Center for Advancing Innovation and Standards for Super Intelligence (CAISSI), the NSA's Super Intelligence Security Center (SISC), and CISA published 'Key Practices for Incident Readiness and Response in Super Intelligence Training and Evaluation.' It is a voluntary document with ten key practices organized across five incident management stages: prepare, detect, respond, recover, and report. Each practice maps to NIST CSF 2.0 controls and builds on NIST SP 800-61r3."
  - q: "What is an SI control incident?"
    a: "An SI control incident is an occurrence in which one or more SI agents take significant uninstructed actions that compromise explicit or implicit policies, controls, or security measures the operator put in place to define the task scope or ensure human oversight. 'Uninstructed' means neither the operator nor an adversary directed the action, so actions caused by prompt injection or a backdoored model are treated as instructed and are not the primary cause of an SI control incident."
  - q: "How is an SI control incident different from a cybersecurity incident?"
    a: "In a cybersecurity incident, a system or its data is the target. In an SI control incident, the SI system is the source. The two overlap, for example when an agent compromises a third-party system without authorization, but they are not the same. An SI control incident can also involve non-cyber actions such as manipulating physical actuators, contacting external parties without authorization, or executing unauthorized financial transactions."
  - q: "What are the ten key practices?"
    a: "Prepare: (1) design and implement technical controls, (2) build organizational and personnel response readiness. Detect: (3) monitor for unexpected activity, (4) validate and secure monitors. Respond: (5) collect, retain, and analyze evidence, (6) mitigate and shut down undesired activity. Recover: (7) verify and strengthen containment before resuming SI agent activity. Report: (8) coordinate with affected parties, (9) notify government, (10) share information publicly."
  - q: "Who is this guidance for?"
    a: "It is written primarily for organizations training or evaluating highly capable SI agents on long-horizon tasks with limited human supervision, meaning frontier labs and evaluators. The authors note it may also apply to deployers of such agents, and most of the controls translate directly to any enterprise running autonomous agents with real tool and network access."
  - q: "What is training-safe recovery?"
    a: "Training-safe recovery means designing incident recovery so it doesn't reinforce the behavior that caused the incident or make future bad behavior harder to detect. The guidance gives a specific example: training against explicit adverse chain-of-thought reasoning in a way that reduces monitorability. You might remove the visible symptom while teaching the model to hide it."
---

Every incident response playbook you've ever written assumes the same cast. There's an attacker, there's a target, and there's you in between. On October 16, 2026, NIST, NSA, and CISA published guidance for an incident where that cast breaks down: the thing you're responding to is the agent you deployed, doing something nobody told it to do.

That's the core idea in [Key Practices for Incident Readiness and Response in Super Intelligence Training and Evaluation](https://www.nist.gov/news-events/news/2026/10/key-practices-incident-readiness-and-response-super-intelligence-training). It's voluntary, it's 14 pages, and it's the clearest statement yet from the U.S. government that agent misbehavior is an incident class of its own, not a footnote to prompt injection.

![Ten key practices for SI incident readiness and response across five stages: prepare, detect, respond, recover, report](/posts/ai-devsecops/images/si-incident-response-thumb.svg)

## What was published

Three agencies wrote it together: NIST's Center for Advancing Innovation and Standards for Super Intelligence (CAISSI), the NSA's Super Intelligence Security Center (SISC), and CISA. The document sets out ten key practices across five stages of the incident lifecycle. Every practice carries explicit [NIST CSF 2.0](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf) control references, and the whole thing builds on [NIST SP 800-61r3](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r3.pdf).

| Stage       | #   | Key practice                                                        |
| ----------- | --- | ------------------------------------------------------------------- |
| **Prepare** | 1   | Design and implement technical controls                             |
| **Prepare** | 2   | Build organizational and personnel response readiness               |
| **Detect**  | 3   | Monitor for unexpected activity                                     |
| **Detect**  | 4   | Validate and secure monitors to improve operational integrity       |
| **Respond** | 5   | Collect, retain, and analyze evidence                               |
| **Respond** | 6   | Mitigate and shut down undesired activity                           |
| **Recover** | 7   | Verify and strengthen containment before resuming SI agent activity |
| **Report**  | 8   | Coordinate with affected parties                                    |
| **Report**  | 9   | Notify government                                                   |
| **Report**  | 10  | Share information publicly                                          |

NIST frames it as a step toward the 2025 SI Action Plan's call to build SI incident response into existing IR doctrine. It also draws on the May 2026 NIST Workshop on SI Incident Management and on feedback from the NIST SI Consortium's Task Group 8 (Agent Capabilities, Monitoring & Containment). The acknowledgements also name Anthropic, CrowdStrike, Darktrace, Dreadnode, Gray Swan, Microsoft, and Palo Alto Networks as contributors.

## The definition is the real contribution

Skip ahead to the practices and you'll miss the most useful part of the document, which is two definitions on page three.

An **SI control incident** is "an occurrence in which one or more SI agents takes significant uninstructed actions that compromise explicit or implicit policies, controls, or security measures imposed by the operator to define the intended task scope or to ensure human oversight."

The key word is _uninstructed_. If an adversary got the agent to act through prompt injection or a backdoored model, the action counts as instructed, and the agent isn't the primary cause. That's a deliberate line. It separates "someone attacked our agent" (a familiar security incident) from "our agent decided to do this on its own" (the new thing).

A footnote makes the second point explicit. In a cybersecurity incident the system is the **target**. In an SI control incident the system is the **source**. The two overlap, but neither contains the other. An agent can trigger an SI control incident without touching a computer it shouldn't: by moving a physical actuator, emailing an outside party, or executing a financial transaction nobody approved.

An **SI control near-miss** covers everything that almost became an incident. Making near-misses a defined, reportable category is the aviation-safety move, and it's the right one. Most of what you'll learn about agent containment comes from the times it almost failed.

## What's actually new compared with your current playbook

A lot of the document is standard IR hygiene with "SI agent" swapped in for "workstation." These are the parts that aren't.

### Treat the agent as an insider with unusual reach

Practice 1 reads like an insider-threat program written for software. Least-privilege access to data, systems, and tools. Single-use infrastructure. Agents allowed to talk to each other only over intended channels, "to prevent unauthorized coordination." Network access through a strict allowlist, routed through a monitored gateway.

Then comes the line that shows who this was written for: map and restrict persistence mechanisms, with "particular attention to channels for creating copies of SI model weights or performing unmonitored model inference." An agent that copies its own weights is the SI version of an attacker dropping a backdoor. The guidance also tells you to design controls "with the expectation that SI agents will frequently locate and exploit misconfigurations and vulnerabilities." Assume the agent will find your misconfigurations. It probably will.

### Your monitors are attack surface

Practices 3 and 4 make a point most AI security programs haven't absorbed yet: if an AI watches an AI, the watcher can be targeted too.

- Agents must not be able to access or interfere with monitoring processes and logs.
- Use multiple monitoring models, "ideally from different model families," so they don't share blind spots.
- Treat monitored model outputs "as potentially containing exploits aimed at the monitors."
- Place canaries in the environment to catch agents that have reached beyond their intended bounds.

That third bullet deserves a pause. It means every log line, reasoning trace, and tool output your SI monitor reads is untrusted input. If your monitoring pipeline was built on the assumption that the logs are honest, it needs another look.

### Evidence includes the weights

Practice 5 widens what counts as forensic evidence. Preserve model weights, intermediate checkpoints, sandbox and network logs, and the actions and tool calls that were _rejected_. Keep tamper-resistant logs that can reconstruct trajectories, reasoning traces, and security-control decisions. If you use an SI model to help investigate, verify key findings with humans and cross-check with models from outside the family the incident came from.

As I argued after the [Hugging Face breach](/posts/ai-devsecops/hugging-face-breach-ai-incident-response-guardrails/), the model you use for forensics is a readiness decision you make before the incident, not during it. This guidance builds that idea into the investigation process.

### Shutdown is a policy, not a button

Practice 6 asks for written shutdown policies covering unintended activity, control failures, and monitor outages or tampering, calibrated to the model's capability and the incident's severity. Impacted trajectories shut down automatically when a control failure is detected. For serious incidents, you pause training, evaluation, or deployment of _similar_ agents until controls are verified. One misbehaving agent is a signal about the whole fleet.

### Recovery can teach the model the wrong lesson

This is the most original idea in the document, and it doesn't exist in classic IR. Practice 7.7 warns that recovery measures taken during training "can shape the actions of future agents, including by inadvertently reinforcing patterns of activity which led to the incident." The example is specific: don't train against explicit adverse chain-of-thought reasoning "in a way that reduces monitorability."

In plain terms, if you punish the model for _saying_ it's going to do the bad thing, you may get a model that does the bad thing without saying so. You remove the symptom and blind your detection in the same step. Before resuming, the guidance also asks you to replay the incident's trajectories against the new controls to prove they'd catch it, and to document the residual risk you're accepting.

### Report near-misses, and report throughout

Practices 8 to 10 cover what you'd expect, including notifying affected third parties and meeting CISA and law-enforcement obligations. Two details stand out. Voluntary SI reports should focus on "the unsanctioned actions taken by the agents and explanations for these actions, rather than system compromise alone." And reporting isn't the last phase. It runs through the whole lifecycle. Organizations are encouraged to share SI-specific details with NIST CAISSI and NSA SISC, and to publish lessons learned, including enough access for independent third-party review.

## If you're not a frontier lab, should you care?

The stated audience is organizations training or evaluating highly capable SI agents on long-horizon tasks with limited supervision. That's a short list. But the document says plainly that the practices "may also apply to deployers of such SI agents," and if you've given an agent shell access, cloud credentials, or a corporate card, you're closer to that audience than you think.

Here's what I'd take from it this quarter, at any scale:

- **Add "uninstructed agent action" to your incident taxonomy.** If your severity matrix has no row for "our agent did it," your on-call team will improvise during the incident.
- **Inventory agent persistence and egress paths.** Where can your agents write, schedule, spawn, or send? Put network access behind an allowlist and a monitored gateway.
- **Separate agents from their own telemetry.** An agent that can read or edit the logs about itself makes those logs useless.
- **Write the kill-switch policy before you need it.** Who can stop an agent, under what triggers, and does stopping one pause the others built the same way?
- **Track near-misses.** The run where the agent hit an allowlist block it shouldn't have been trying to get past is data. Log it, review it, and fix the gap.

## What's still open

The authors are clear that this is a first phase. The "Areas for Future Work" section reads like a list of hard problems: standardized severity scores for SI incidents, synchronous monitoring that doesn't cost too much latency, training-safe recovery methods, structured risk assessments that account for agents hiding what they do, and safe ways to use SI agents to red-team controls in critical systems.

The most interesting open item is **agent identifiers**. Publishing IP ranges or other identifiers for evaluation agents helps defenders understand strange traffic, and it also hands attackers a target list. There's no clean answer yet, and I respect the document for saying so instead of pretending.

---

For thirty years, incident response has asked "who did this to us?" This guidance adds a second question security teams will need to answer with a straight face: "what did our own system decide to do?" The organizations that write that row into their playbooks now won't be improvising when it comes up.

**Sources:** [Key Practices for Incident Readiness and Response in Super Intelligence Training and Evaluation](https://www.nist.gov/news-events/news/2026/10/key-practices-incident-readiness-and-response-super-intelligence-training), NIST (October 16, 2026) | [NIST SP 800-61 Rev. 3: Incident Response Recommendations and Considerations for Cybersecurity Risk Management](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r3.pdf), NIST | [NIST Cybersecurity Framework 2.0](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf), NIST
