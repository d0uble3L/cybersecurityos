---
title: "Non-Human Identity (NHI) 101: What It Is and Why It's Suddenly Everywhere"
date: 2026-07-19
draft: false
author: "Michael Tayo"
tags:
  [
    "Non-Human Identity",
    "Machine Identity",
    "Service Account Security",
    "NHI Management",
    "AI Security",
    "Identity and Access Management",
    "Zero Trust",
  ]
categories: ["Identity Security", "Security Engineering"]
description: "A plain-English guide to non-human identities — what they are, why they now outnumber human accounts, and how to start securing them."
show_reading_time: true
featured_image: /posts/security/images/nhi-thumb.svg
keywords: "non-human identity, non-human identity security, machine identity, service account security, NHI management, agentic AI identity, API key security"
faq:
  - q: "What is non-human identity security?"
    a: "Non-human identity security is the practice of managing, monitoring, and protecting credentials used by software systems rather than people — including service accounts, API keys, bots, IoT devices, and AI agents. It encompasses discovery, least-privilege access enforcement, credential rotation, lifecycle management, and anomaly detection for machine-to-machine authentication."
  - q: "How are non-human identities created?"
    a: "Non-human identities are typically created when a developer provisions a service account, generates an API key, registers a device certificate, or deploys an automation script that needs to authenticate to another system. Many are created informally — a quick API key grabbed from a dashboard, a service account spun up to meet a deadline — without the same governance process that governs human account creation."
  - q: "How many machine identities exist per human identity?"
    a: "Industry estimates vary, but most research puts the ratio at 45 machine identities for every human identity in a typical enterprise, with some organizations reporting even higher ratios as cloud adoption and automation increase. The number grows every time a new microservice, automation script, or AI agent is deployed."
  - q: "Are AI agents considered non-human identities?"
    a: "Yes. An AI agent that authenticates to APIs, accesses databases, reads emails, or calls external services does so using credentials — API keys, OAuth tokens, or service accounts. Those credentials are non-human identities, and they carry the same risks as any other machine credential: over-permissioning, lack of rotation, and orphaning when the project that created them is discontinued."
  - q: "What is the difference between NHI and human identity management?"
    a: "Human identity management is built around a person's lifecycle — hire, role change, offboard. Non-human identity management must handle a much larger volume, shorter lifespans, no off-boarding trigger tied to a person leaving, and credentials that are often embedded in code or configuration files rather than stored in a directory. Traditional IAM tools weren't built for this scale or pattern."
---

Every time you log into a system, you authenticate with a credential — a username and password, a passkey, an SSO session. Now consider how many times your cloud infrastructure, CI/CD pipeline, monitoring tools, microservices, and AI agents do the same thing — without a person involved. At most enterprises, that number is staggering, and it's growing faster than most security teams realize.

This is the non-human identity problem. It's not new, but it's suddenly urgent — and if you're trying to understand what NHI actually means and why it keeps showing up in security conversations, this is the guide for you.

## What Is a Non-Human Identity?

A non-human identity (NHI) is any digital credential used by software, a machine, or an automated process — rather than a person — to authenticate to a system or access data.

Think of it like a badge, but for software. When you badge into an office, you're a human with an identity tied to HR records, a lease, a photo. When a microservice calls another microservice, it also "badges in" — using an API key, a service account token, a certificate, or an OAuth credential. That credential is a non-human identity.

What makes NHIs distinct is the absence of a human in the loop. There's no one logging in, no session a person initiates, and often no one monitoring whether the credential is still necessary, correctly scoped, or being used as intended.

## Types of Non-Human Identities

Non-human identities come in several forms, each with its own risk profile.

### Service Accounts

Service accounts are user accounts created specifically for applications or automated processes rather than people. They're common in Active Directory and cloud IAM systems, and they're frequently granted broad permissions at the time of setup — and then forgotten. When an employee who created the service account leaves, the account often stays, with no clear owner.

### API Keys and Tokens

API keys and tokens are static secrets that grant a service access to an API. They're everywhere: in `.env` files, CI/CD pipelines, application configs, and sometimes committed directly to version control by accident. Unlike user sessions, API keys don't expire on their own, don't enforce MFA, and can't be traced back to a person — just the application (if you're lucky) or a long-dead project (if you're not).

### Bots and RPA Scripts

Robotic process automation (RPA) bots authenticate to systems to perform repetitive tasks: scraping data, processing invoices, updating records. They often run under service accounts or hardcoded credentials, and they touch sensitive systems at scale — making a compromised RPA credential a high-value target.

### IoT and Device Identities

Every internet-connected device — a sensor, a camera, a networked medical device, a factory controller — has an identity it uses to communicate with backend systems. These credentials are often embedded in firmware, rarely rotated, and difficult to audit across large device fleets. IoT credentials are a favorite persistence mechanism for nation-state actors because they're easy to overlook.

### AI Agents

This is the fastest-growing category — and the one most security teams are least prepared for. An AI agent that browses the web, reads emails, calls APIs, queries databases, or executes code does so using credentials. Those credentials are non-human identities with real access to real systems. Unlike a static automation script, AI agents can take unexpected actions based on the content they encounter, which makes the permissions they hold especially important to scope tightly.

![Illustration of an AI agent identity card alongside a digital figure and scales of justice, representing Estonia's proposal for state-issued IDs for AI agents](/posts/security/images/estonia-ai-agent-state-ids.png)

The identity question is no longer purely technical, either. Estonia has floated the idea of state-issued IDs for AI agents — a legal precedent that treats agentic AI as an entity that can hold a verifiable, government-recognized identity, distinct from the human or organization that deployed it. Whether or not that specific proposal goes anywhere, it signals where this is headed: NHI governance is starting to intersect with legal and regulatory identity frameworks, not just IAM tooling.

If you're interested in the specific risk profile of AI agents as an attack surface, the [GPT-Red research I covered earlier](/posts/ai-devsecops/gpt-red-self-improvement-robustness/) is a good illustration of how adversaries are already thinking about this.

## Why Non-Human Identities Are Multiplying

Three forces are driving the growth, and they're all accelerating at the same time.

**Cloud and microservices adoption** fragmented monolithic applications into dozens or hundreds of services, each of which needs credentials to communicate with the others. Where a traditional three-tier app might have had a handful of service accounts, a modern cloud-native deployment can have hundreds before the first user signs up.

**Automation and DevOps** added another layer: CI/CD pipelines, infrastructure-as-code runners, test harnesses, deployment bots, monitoring agents. Each needs access to production systems to do its job. Each is a non-human identity that needs to be managed.

**Agentic AI** is the newest and most rapid contributor. Every AI agent you deploy — whether it's a customer service bot, a security triage assistant, or an autonomous coding agent — authenticates somewhere. The more capable the agent, the broader the access it typically needs, and the harder it is to scope that access in advance.

The net result: most enterprises now have somewhere around 45 machine identities for every human identity in their environment. In organizations with heavy automation or large AI deployments, that ratio is often much higher.

## Why Non-Human Identities Are Risky

The risk isn't just that NHIs exist — it's that they're managed (or not managed) very differently from human accounts, and traditional security controls weren't built with them in mind.

**Over-permissioned by default.** When a developer creates a service account under deadline pressure, they grant it what it needs to work — plus a margin. That margin compounds across hundreds of accounts. The principle of least privilege is easy to articulate and hard to enforce when you're shipping fast.

**Rarely rotated or expired.** Human passwords expire. Sessions time out. NHI credentials often don't. An API key generated three years ago for a proof-of-concept might still be valid, still have production access, and still be sitting in a config file no one has looked at since the engineer who wrote it left the company.

**No clear owner after turnover.** Human accounts are tied to a person — when they leave, IT knows to deprovision. Non-human identities are tied to a project, a team, or sometimes just the judgment call of whoever created them. When that context disappears, the credential doesn't.

**Invisible to traditional IAM tools.** Most identity governance tools were designed around directory services and human lifecycle events. They don't naturally surface orphaned service accounts in AWS, stale API keys in GitHub Secrets, or over-permissioned tokens in a Kubernetes cluster. You need different tooling — or at minimum, different queries — to find them.

> **The uncomfortable stat:** Studies across large enterprises consistently find that 50% or more of active non-human identities are either over-permissioned, orphaned, or were created with no documented owner. That's not a technology problem — it's a governance gap.

## Real-World Consequences

The breach cases where NHI credentials played a role tend to follow a predictable pattern: a credential was created for a legitimate purpose, was never rotated or scoped down, and eventually ended up somewhere an attacker could find it.

API keys committed to public GitHub repositories are among the most common. Automated scanners continuously monitor public repos for secrets — and a surprising number of organizations have learned about a production credential exposure from a third-party alert rather than internal detection.

Orphaned service accounts — ones that still have production database access but no one knows why they were created — show up in post-incident reports from ransomware investigations more often than the victim organizations would like to admit. Attackers doing internal reconnaissance specifically look for accounts that aren't monitored and have broad access. Orphaned service accounts fit that profile exactly.

The pattern with IoT credentials is similar: embedded secrets in firmware, default credentials that were never changed, or device certificates that were never revoked after the device was decommissioned. These become persistent footholds that survive incident response if the responder doesn't know to look for them.

## How Organizations Are Securing Non-Human Identities

The good news: the problem is solvable. The bad news: it requires discipline across tooling, process, and culture that most organizations are still building.

**Discovery and inventory first.** You cannot protect what you cannot see. The first step in any NHI security program is getting a complete picture of what credentials exist, where they're used, what they have access to, and who (if anyone) owns them. This is harder than it sounds — NHIs are scattered across cloud consoles, code repositories, CI/CD systems, and vault solutions that may or may not be integrated with each other.

**Least-privilege access.** Every NHI should have exactly the access it needs to do its job — not the access that was convenient to grant. For AI agents specifically, this means scoping tool permissions per task rather than granting broad read/write access to entire systems.

**Automated credential rotation.** Static secrets are a liability. Any credential that doesn't expire is a credential that, if compromised, stays compromised. Secrets management platforms (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault) make automated rotation tractable — the key is adopting them consistently rather than selectively.

**Ownership assignment and lifecycle management.** Every NHI should have an owner on record: a team, a service, a named engineer. When that owner changes or the project ends, there should be a process — automated where possible — to review or revoke the credential. Treating NHIs with the same lifecycle rigor as human accounts closes the orphan account problem over time.

**Dedicated NHI management platforms.** A growing category of tools (Astrix, Entro, Clutch, and others) is specifically designed to discover, classify, and govern non-human identities across cloud environments, SaaS platforms, and code repositories. For organizations with significant scale, these are worth evaluating — generic IAM tools tend to miss the long tail of machine identities.

## Non-Human Identity vs. Human Identity Management: Key Differences

|                           | Human Identity                          | Non-Human Identity                                   |
| ------------------------- | --------------------------------------- | ---------------------------------------------------- |
| **Volume**                | Hundreds to thousands                   | Tens of thousands to millions                        |
| **Lifecycle trigger**     | HR events (hire, role change, offboard) | Project creation/deprecation — often informal        |
| **Authentication method** | Password, MFA, SSO                      | API key, token, certificate, service account         |
| **Expiration**            | Password policies, session timeouts     | Often none by default                                |
| **Owner**                 | The person                              | A team, a project, sometimes no one                  |
| **Monitoring**            | Behavioral analytics, login anomalies   | Largely absent in most orgs                          |
| **IAM tooling**           | Mature — built for this                 | Emerging — most tools weren't designed for NHI scale |

The core challenge is that human identity management was built for a world where every identity has a face and a manager. NHI management has to work for a world where most identities are invisible, don't have managers, and outnumber human accounts by an order of magnitude.

## FAQ

### What is non-human identity security?

Non-human identity security is the practice of managing, monitoring, and protecting credentials used by software systems — service accounts, API keys, bots, IoT devices, and AI agents. It covers discovery, least-privilege enforcement, credential rotation, lifecycle management, and anomaly detection for machine-to-machine authentication.

### How are non-human identities created?

Most are created informally: a developer provisions a service account, generates an API key from a dashboard, or registers a device certificate. Unlike human account creation, which typically goes through an HR or IT workflow, NHIs are often created at the point of need without a formal governance process.

### How many machine identities exist per human identity?

Industry estimates put the ratio at roughly 45 machine identities per human identity in a typical enterprise. Organizations with heavy cloud adoption, microservices architectures, or active AI agent deployments often see higher ratios.

### Are AI agents considered non-human identities?

Yes. An AI agent that authenticates to APIs, accesses databases, reads emails, or calls external services does so using credentials — and those credentials are non-human identities. The risk profile is especially important to manage because AI agents can take unexpected actions based on the content they encounter, which makes the scope of their access particularly consequential.

### What is the difference between NHI and human identity management?

Human identity management is built around a person's lifecycle and is well-supported by mature tooling like Active Directory and enterprise SSO platforms. NHI management must handle larger volume, shorter or undefined lifespans, no off-boarding trigger tied to a person leaving, and credentials embedded in code or infrastructure rather than stored in a directory. Most traditional IAM tools were not built for this problem.

## Conclusion

The visibility principle holds everywhere in security: you can't protect what you can't see. Non-human identities are the largest blind spot in most organizations' identity programs — not because the problem is new, but because the scale and variety of machine credentials has outpaced the tooling and processes designed to manage them.

The place to start is inventory. Know what credentials exist, where they're used, what they have access to, and who owns them. Everything else — least privilege, rotation, lifecycle management, dedicated NHI tooling — follows from that foundation.

As agentic AI deployments grow, this problem is only going to compound. An AI agent with over-permissioned credentials is a much larger blast radius than a misconfigured service account, because agents act autonomously and at speed. Getting NHI governance in place now is the kind of foundational work that will make or break AI security programs over the next few years.

For more on the intersection of AI and identity security, see [GPT-Red: What OpenAI's Automated Red-Teamer Means for AI Security](/posts/ai-devsecops/gpt-red-self-improvement-robustness/) and [AWS's Security Agent](/posts/ai-devsecops/) for related coverage on how AI is changing both the attack and defense landscape.
