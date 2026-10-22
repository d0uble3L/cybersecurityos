---
title: "The Bottleneck Moved: Detection Engineering Heading Into 2027"
date: 2026-10-13
draft: false
author: "Michael Tayo"
tags:
  [
    "Detection Engineering",
    "SOC",
    "AI Agents",
    "Detection-as-Code",
    "Security Leadership",
    "Security Metrics",
    "Cloud Security",
    "Threat Detection",
  ]
categories: ["Security Operations", "Security Leadership"]
description: "The detection bottleneck moved from writing rules to investigating alerts. What AI agents change, how to measure it, and five predictions for 2027."
slug: "detection-engineering-2027-bottleneck-moved"
show_reading_time: true
featured_image: /posts/secops/images/detection-engineering-2027-social.png
images:
  - /posts/secops/images/detection-engineering-2027-social.png
keywords: "detection engineering 2027, detection engineering trends, AI SOC analysts, AI agents alert triage, detection-as-code adoption, glass-box automation SOC, detection engineering metrics, ATT&CK coverage vanity metric, SANS State of Detection Engineering 2026, M-Trends 2026, monitoring AI agents non-human identities, in-stream detection data pipelines"
faq:
  - q: "What is the biggest bottleneck in detection engineering heading into 2027?"
    a: "The constraint has shifted from signal creation, writing enough good detection rules, to signal consumption: who or what investigates each alert, and how quickly. With initial access handed off between threat actors in a median of about 22 seconds according to Mandiant M-Trends 2026, a detection that fires but waits in a queue has still failed."
  - q: "How should detections be written for AI agents?"
    a: "A detection an AI agent will investigate should carry its reasoning explicitly: environmental context, the evidence to collect, expected benign explanations, an ATT&CK technique mapping, the conditions that make it high or medium confidence, and response guidance including when to escalate to a human. A detection complete enough for an agent is also better for human analysts."
  - q: "Why does Detection-as-Code adoption stall?"
    a: "Mostly because of skills, not tools. The SANS State of Detection Engineering 2026 found software engineering proficiency to be the biggest foundational skills gap. Teams without habits like code review, testing, and working in CI pipelines struggle to run Detection-as-Code regardless of which platform they buy."
  - q: "Is ATT&CK coverage percentage a good detection metric?"
    a: "On its own, no. Coverage counts whether a rule exists for a technique, not whether it fires correctly or leads to the right action. Better metrics include time from threat intelligence to a deployed detection, the percentage of alerts that drive a correct action, and the cost of false positives in analyst hours."
  - q: "Do AI agents in the SOC need to be monitored?"
    a: "Yes. SOC agents are privileged non-human identities with access to sensitive logs and often the ability to disable accounts, revoke tokens, or close alerts. Teams should baseline each agent's normal tool use, data access, and actions, alert on deviations, and log agent activity as rigorously as administrator activity."
---

Twenty-two seconds. That's the median time, per Mandiant's M-Trends 2026, for initial access to be handed off from one threat actor to the next. One crew gets in. Another takes over.

Now look at your queue. An alert fires. It waits. Someone picks it up, pivots across four consoles, decides it's probably the backup job again, and closes it. The rule worked. The detection still failed.

That gap is the story of detection engineering heading into 2027. For a decade the constraint was signal creation. Could we write enough good rules, fast enough, across enough telemetry? That problem hasn't gone away. But it's no longer the binding constraint. The bottleneck moved to signal consumption: who or what investigates the alert, and how fast.

AI agents are entering that consumption layer. That changes who we write detections for, how we measure them, and what the detection engineer's job rewards.

## Where the Field Really Stands

The SANS State of Detection Engineering 2026 surveyed 307 practitioners across more than ten industries. 80% say they're barely keeping pace or falling behind. Only 18% say they're ahead.

Put that next to the threat data. M-Trends 2026 has exploitation as the top initial access vector for the sixth year running, at 32%. Exploitation moves fast at the edge and goes quiet once it lands. It punishes teams that can detect but can't act.

Two more SANS findings shape everything below. Cloud-native environments are the number one coverage gap, named 2.5 times more often than any other environment. And software engineering proficiency is the biggest foundational skills gap.

Meanwhile, Gartner has been reported as expecting 75% of SOCs to deploy AI analysts by the end of 2026. The exact number matters less than the direction. A large share of your alerts will be read first by something that isn't a person.

## Detections Now Have Two Audiences

![AI analyst robot working through security alerts at a laptop](https://www.dropbox.com/scl/fi/1hus1ym57knkej6slo1n6/Enhancing-AI-agents-cybersecurity-skills.png?rlkey=c81h5uuwa1c2v646x9f5hj85v&raw=1)

Most detections are written for a human who already knows the environment. The title is terse. The description is one sentence. The context lives in the analyst's head.

An agent doesn't have that head. It has what you give it and whatever it can query. If the detection is thin, the agent fills the gap with guesses. A confident wrong answer is worse than a slow right one.

Take an OAuth consent-phishing detection. Written for a human, it reads: "User granted high-risk permissions to an unverified third-party app." A good analyst knows what to do next. Check the publisher. Check the scopes. See whether anyone else consented. Look for a suspicious email right before the grant.

Written for an agent, the detection has to carry that reasoning itself. Context: the tenant, the user, the user's role, and whether this app has ever been seen in the organization. Evidence: the consent event, the granted scopes, the publisher verification status, and the sign-in that preceded the grant. Expected benign explanations: a sanctioned app IT rolled out this week, a developer testing their own integration, a known vendor whose verification lapsed. ATT&CK mapping: T1528, Steal Application Access Token, with phishing as the likely delivery. Confidence: the specific conditions that make this high versus medium, written as logic, not adjectives. Response guidance: revoke the grant, disable the service principal, review the user's mailbox activity, and the exact conditions for handing off to a human.

None of this is new knowledge. Good analysts have always used it. What's new is that it has to live inside the detection, in structured form, because the first reader can't walk over and ask the author a question.

Here's a position some will push back on: write the detection for the agent first. A detection complete enough for an agent is better for humans too. The reverse isn't true. Teams that keep writing for humans and bolt on "agent context" later will end up maintaining two drifting versions of the same logic.

Expected benign explanations are the field most teams skip. They're also the one that matters most. An agent that knows what normal looks like can close the backup-job alert correctly and say why. An agent that doesn't will either escalate everything or start closing things that merely look similar.

## Glass Box, Not Black Box

![AI agents working inside a transparent glass enclosure](https://www.dropbox.com/scl/fi/u80fel3eejspbjeteph9n/AI-agents-and-cybersecurity-skills.png?rlkey=10i6306vyiqn7ragmnte687hz&raw=1)

You can't tune automation you can't inspect.

When a human closes an alert as benign, you can ask why. When a black-box system closes it, you get a verdict. If the verdict is wrong, you can't tell which part failed: the rule logic, the enrichment, the context, or the model's judgment.

Glass-box automation means every automated decision leaves an evidence trail. What data did it pull? What did it compare against? Which benign explanation did it match, and on which fields? A detection engineer reading that trail can tell whether the rule is noisy, the enrichment is broken, or the agent is simply wrong. Without that trail, you're tuning blind.

It's also a governance problem. If an automated system dismissed an alert that later turned out to be the initial access for an incident, you'll need to show why it made that call. "The model decided" won't survive a regulator, a board, or a post-incident review. In financial services we already had to justify human decisions. Machine decisions will be held to the same standard.

My position: if you can't reconstruct why an alert was closed, it shouldn't be closed automatically. Opacity isn't a tradeoff to weigh against speed. It's disqualifying.

## Why Detection-as-Code Stalls

Detection-as-Code has been the right answer for years. Version control. Peer review. Tests. Pipelines that validate a rule before it reaches production. Most teams agree with it. Far fewer run it.

The usual explanation is tooling. The real one is people. The SANS data says it directly: software engineering proficiency is the biggest foundational skills gap. Hand a team a repo, a linter, and a test harness. If they've never written a unit test, reviewed a pull request, or debugged a failing pipeline, the repo becomes a place where rules get dumped, not engineered.

This matters more now, not less. The agent-ready detection described above is a small software artifact. It has a schema. It has tests, including tests for its benign explanations. Teams without engineering habits will struggle to produce it no matter which platform they buy.

The fix is slow and unglamorous. Pair analysts with engineers. Make code review a normal part of shipping a rule. Hire for the skill or train for it on purpose, with time on the calendar. A tool purchase is not a skills plan.

## Detection Moves Toward the Data

![Shield over a network of connected data nodes and pipelines](https://www.dropbox.com/scl/fi/zvrggemlkwon90klqls69/Forensic-readiness-for-AI-containment-breaches.png?rlkey=b5ofqn2lzi2qqdvd7uo8z23aw&raw=1)

The old assumption was simple: send everything to the SIEM and detect there. That assumption is breaking, mostly on cost.

Detection is moving closer to the data. Some of it runs in the pipeline as events stream in, before anything is indexed. Some runs directly against data lakes where security telemetry sits next to everything else. The SIEM becomes one place detection happens, not the only one.

Two things follow. First, cost. You stop paying premium ingest for high-volume, low-value logs just to run a handful of rules against them. Filter, enrich, and detect in-stream. Forward what matters. Second, coverage. Cloud-native is the top coverage gap, and cloud telemetry is high volume and expensive to centralize. Detecting near where it's generated is one of the more realistic ways to close that gap without breaking the budget.

The catch is governance. Rules now live in pipeline configs, lake queries, and SIEM content. Without Detection-as-Code discipline, nobody knows what's deployed where.

## The New Target: Your Own Agents

Most programs haven't started on this one. The AI agents you deploy in the SOC are privileged non-human identities. They read sensitive logs. Many can act: disable accounts, revoke tokens, isolate hosts, close tickets.

That makes them a target. An attacker who can influence an agent's inputs, through a crafted log entry, a poisoned ticket description, or a tampered enrichment source, may be able to steer its decisions. An agent that can close alerts can close the alert about itself.

So detect on your own agents. Baseline what each one normally does: which tools it calls, which data it reads, which actions it takes, and how often. Alert on deviation. Watch for actions outside its scope, spikes in closures, or reads of data it has never touched. Log every agent action with the rigor you'd apply to a domain admin.

Who watches the watchers? Your detection engineers do. It's a new detection domain, and it belongs on the roadmap now.

## The Leader Lens

Detection leaders lose budget arguments because they report in the wrong language. "Our ATT&CK coverage went up this quarter" means nothing to a CFO.

Use a simple chain: technical fact, business risk, business impact. Technical fact: we can't detect OAuth consent abuse in our cloud tenant. Business risk: an attacker could get persistent access to executive mailboxes without ever needing a password. Business impact: exposed deal communications, regulatory notification, and lost client trust. Now you're talking about risk the business already understands.

Which brings me to the claim I expect the most pushback on: coverage percentage is a vanity metric. It measures whether a rule exists for a technique, not whether that rule fires correctly, gets investigated, or drives the right action. You can raise coverage by shipping weak rules nobody acts on. It rewards rule count over outcomes, and in a consumption-bound world, that's measuring the wrong end of the pipe.

Three metrics that measure what matters:

**Time from intel to deployed detection.** When credible threat intelligence lands, how long until a tested detection is in production?

**Percentage of alerts driving a correct action.** Not closed. Not escalated. Correct. It requires reviewing a sample of decisions, human and agent alike. It's the metric that tells you whether consumption works.

**False-positive cost in analyst hours.** Turn noise into time, and time into money. This is the number that funds tuning work. It also shows whether agents are reducing load or just moving it somewhere else.

## Five Predictions for 2027

Each of these should be checkable in December 2027.

- **At least one widely used open detection rule format will formally add fields for agent investigation context**, such as expected benign explanations or response guidance.
- **At least one publicly disclosed incident will involve an attacker manipulating a SOC AI agent**, through injected content in logs, tickets, or enrichment data, to suppress or close alerts.
- **Cloud-native will still be the top coverage gap** in the next SANS State of Detection Engineering survey. Tooling won't close a skills-and-telemetry gap in a year.
- **At least one financial services regulator will publish guidance that explicitly addresses the explainability or auditability of AI-driven security alert triage.**
- **Detection engineer job postings will list software engineering skills** (version control, testing, CI/CD) as requirements more often than expertise in any single SIEM query language.

## Monday Morning

For practitioners:

- Take your five noisiest detections. Write the expected benign explanations into each rule itself, not a wiki.
- Rewrite one detection end to end for an agent: context, evidence, benign explanations, ATT&CK mapping, confidence conditions, response guidance. Give it to a colleague who's never seen it. Can they triage with nothing else?
- Put one rule through real code review with a real test. If your team can't do that yet, that's your finding.

For leaders:

- Replace coverage percentage in your next report with one of the three metrics above. Start with false-positive cost in analyst hours.
- Before any AI analyst goes to production, require a reviewable evidence trail for every automated closure. No trail, no automation.
- Inventory every AI agent with access to your security tooling. Give each one an owner, a defined scope, and detections on its behavior.

## One Question

If an agent closed an alert in your SOC yesterday, could you explain to your regulator today exactly why?

---

## Tools I Use

A quick aside from me, separate from the argument above. These aren't detection platforms; they're what I use around the work.

**[Make](https://www.make.com/en/register?pc=trilltayo)** is my glue layer for the boring parts: CVE feeds filtered by severity into a morning digest, IOC enrichment against WHOIS and VirusTotal, alerts routed to chat. It's a fast way to prototype a SOAR-lite playbook before you decide whether it deserves real engineering time. (Affiliate link: it supports CybersecurityOS at no cost to you.)

**[Canva](https://www.canva.com/join/vqp-gcy-fgx)** is where I build the one-pagers that carry the "technical fact, business risk, business impact" chain to leadership: threat briefs, ATT&CK overlays, incident timelines. If your detection work isn't getting budget, the problem is often the slide, not the rule. (Affiliate link: it supports CybersecurityOS at no cost to you.)

**[Hypefury](https://hypefury.com/?via=trilltayo)** keeps my writeups and lab notes going out on a schedule, so sharing what I learn doesn't eat into the time I spend learning it. Publishing your detection work is one of the better ways to build a reputation in this field. (Affiliate link: it supports CybersecurityOS at no cost to you.)
