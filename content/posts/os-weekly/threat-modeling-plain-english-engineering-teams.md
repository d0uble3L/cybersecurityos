---
title: "Threat Modeling in Plain English: A Guide for Engineering Teams"
date: 2026-05-31
draft: false
author: "Michael Tayo"
tags: ["threat modeling", "cybersecurity", "security engineering", "STRIDE", "DevSecOps", "CybersecurityOS", "appsec", "secure by design"]
categories: ["Security Engineering"]
slug: "threat-modeling-plain-english-engineering-teams"
description: "A practical, no-jargon guide to threat modeling for engineering teams. Learn STRIDE, how to run your first threat model, and how to make it a sustainable part of your SDLC — not a one-time exercise."
images:
    - /posts/os-weekly/images/process.png
featured_image: /posts/os-weekly/images/process.png
---

Most engineering teams know they *should* be doing threat modeling.

Very few actually do it — and the ones who try often produce a document that gets filed away and never looked at again.

The problem isn't motivation. It's that almost every guide to threat modeling is written for security teams, not engineering teams. The language is wrong. The framing is wrong. The process feels like a compliance exercise instead of something that makes the software actually harder to attack.

This guide fixes that.

By the end, you'll understand what threat modeling is, how to run one in a single session, and how to make it a repeatable part of how your team ships software.

---

## What Threat Modeling Actually Is

Threat modeling is structured thinking about how your system could be attacked — done before the attack happens.

That's it. No special tools required. No security certification needed. No month-long process.

The core question is: **"If someone wanted to break this, how would they do it?"**

You ask that question about your system, you write down the answers, and you decide which ones to fix. Everything else is process around that.

> // Threat modeling is not a penetration test. It's not a compliance checkbox. It's not a security team deliverable. It's an engineering practice — like code review, but for trust assumptions.

---

## Why Most Teams Skip It (And Why That's Expensive)

The most common objection: *"We don't have time."*

The real cost of not doing it: your team spends hours on a security incident post-mortem for something that was obvious in hindsight. A missing authentication check. An over-privileged service account. An unauthenticated API endpoint that shouldn't be public.

Most vulnerabilities that cause serious incidents aren't sophisticated. They're foreseeable. They show up in threat models. They get fixed for free at design time and they cost enormously when exploited at runtime.

The other objection: *"That's what the security team is for."*

Security teams are too small to be in every design review. The only scalable answer is engineers who can think adversarially about their own systems. That's not optional anymore — it's a core engineering skill.

---

## The Four Questions That Power Every Threat Model

Before any framework, there are four questions. Every threat modeling methodology is ultimately an answer to these four:

```
1. What are we building?
2. What can go wrong?
3. What are we going to do about it?
4. Did we do a good enough job?
```

These are from Adam Shostack's work at Microsoft — the person who helped formalize threat modeling as a discipline. Keep these four questions in your head. When a process gets complicated, come back to them.

---

## The STRIDE Framework: The Engineering Team's Mental Model

STRIDE is the most practical threat modeling framework for engineers. It's a mnemonic that maps to six categories of threats. For each component of your system, you ask whether each threat applies.

![Threat Modeling Process — structured thinking about how a system can be attacked](/posts/os-weekly/images/process.png)

| Letter | Threat | What it means | Example |
|--------|--------|---------------|---------|
| **S** | Spoofing | Impersonating something or someone | Attacker forges a JWT to act as another user |
| **T** | Tampering | Modifying data or code | Attacker alters a query parameter to change an order total |
| **R** | Repudiation | Denying that an action occurred | User denies placing a fraudulent order — no audit log to prove otherwise |
| **I** | Information Disclosure | Exposing data to unauthorized parties | Stack trace in a 500 error leaks internal file paths and versions |
| **D** | Denial of Service | Making a system unavailable | Unauthenticated endpoint hammered with requests; no rate limiting |
| **E** | Elevation of Privilege | Gaining capabilities beyond what's authorized | Standard user accesses admin API endpoint due to missing authorization check |

You don't need to memorize these. Print this table. Keep it in your design docs template. Ask "does STRIDE apply here?" for every trust boundary you cross.

---

> 💡 **Want to go deeper on security engineering frameworks?**
> ![Cybersecurity Leadership OS: Battle-Tested Mental Models for Clarity, Speed & Command](/posts/os-weekly/images/leadershipos.png)
> Built for engineers moving into security leadership — 30+ mental models, decision frameworks, and real-world guides.
> 🔗 [Cybersecurity Leadership OS](https://store.cybersecurityos.net/l/cybersecurity-leadership-os)

---

## Step-by-Step: Running Your First Threat Model

This is a 60–90 minute session. You need a whiteboard (or a Miro/FigJam board), the four people closest to the system, and the four questions above.

### Step 1: Draw the System (15 minutes)

Start with a **Data Flow Diagram (DFD)**. Don't overthink the format — you're looking for:

- **Processes** — things that transform data (your services, functions, jobs)
- **Data stores** — where data lives (databases, caches, S3 buckets, message queues)
- **External entities** — things outside your control (users, third-party APIs, other teams' services)
- **Data flows** — how data moves between them (arrows)
- **Trust boundaries** — where data crosses from one trust zone to another (dashed lines)

The trust boundaries are the most important part. Anywhere data crosses a trust boundary — an API call, a database query, a file read — is where you focus your threat modeling attention.

**Example for a simple REST API:**

```
[User Browser] ──HTTPS──> [API Gateway] ──> [Auth Service]
                                    │
                                    └──> [Orders Service] ──> [Orders DB]
                                                    │
                                                    └──> [Payment API (external)]
```

Trust boundaries: browser → gateway, gateway → internal services, orders service → external payment API.

---

### Step 2: Identify the Threats (30 minutes)

For each trust boundary and each component, work through STRIDE. Go component by component — don't try to analyze the whole system at once.

**Useful prompts to keep the conversation moving:**

- "What happens if an attacker controls the input at this point?"
- "What data lives here, and who isn't supposed to see it?"
- "What breaks if this component goes down?"
- "What's the worst thing someone could do if they had access to this?"
- "Is there a way to do something here without being logged?"

Write down every threat you identify. Don't filter yet. Volume is your friend at this stage — the filtering comes next.

![Identifying and mapping security threats across system components](/posts/os-weekly/images/solutions.png)

---

### Step 3: Rate and Prioritize (15 minutes)

You don't fix everything. You fix what matters most given your context.

A simple rating approach: **DREAD** (Damage, Reproducibility, Exploitability, Affected users, Discoverability) — score 1–3 on each. High total = fix first.

Or simpler: **likelihood × impact**. A missing rate limit on a public API is high likelihood + potentially high impact = fix it now. A theoretical privilege escalation that requires physical access to a server = low likelihood = defer.

The output of this step is a prioritized list. Three columns: **Threat | Severity | Mitigation**.

---

### Step 4: Decide What to Do (10 minutes)

For each identified threat, one of four responses:

1. **Mitigate** — fix it with a control (add authentication, add validation, add logging)
2. **Transfer** — accept it at the organizational level and make sure the right person knows (e.g., cyber insurance covers this scenario)
3. **Avoid** — redesign so the threat doesn't apply (don't collect the data at all)
4. **Accept** — document the residual risk and move on (with sign-off from someone accountable)

Turn your mitigations into tickets before you leave the session. Threat models that don't produce work items get ignored.

---

## A Real Example: Threat Modeling an Authentication Flow

Let's make this concrete. Say you're building a new login flow with email/password + MFA.

**Components:**

- Login form (browser)
- `/auth/login` endpoint
- User credentials database
- MFA service (TOTP/SMS)
- Session token store (Redis)

**Applying STRIDE:**

| Component | Threat | Specific concern | Mitigation |
|-----------|--------|------------------|------------|
| Login form | **S** Spoofing | Attacker creates a fake login page (phishing) | Enforce HTTPS + HSTS; consider passkeys |
| `/auth/login` | **D** DoS | Brute-force / credential stuffing | Rate limiting, account lockout, CAPTCHA |
| `/auth/login` | **T** Tampering | Request body manipulation | Validate all inputs server-side; don't trust client |
| Credentials DB | **I** Info disclosure | SQL injection leaks password hashes | Parameterized queries; bcrypt/Argon2 hashing |
| Session token | **S** Spoofing | Token theft via XSS | HttpOnly + Secure cookie flags; CSP headers |
| MFA service | **E** Elevation | MFA bypass if fallback is weak | No SMS-only fallback for sensitive actions; enforce app-based TOTP |
| Redis session store | **T** Tampering | Session fixation | Regenerate session ID on authentication |

This is a 20-minute exercise for a senior engineer who knows the system. It produces seven specific, actionable security requirements before a line of code is written.

---

## When to Threat Model

**The rule: threat model when the attack surface changes.**

That includes:

- New features that handle new data types
- New external integrations (APIs, webhooks, OAuth flows)
- Changes to authentication or authorization logic
- New infrastructure (new cloud service, new database, new queue)
- Major refactors that touch trust boundaries

A full threat model session for a new system. A focused 20-minute review ("STRIDE against the changed components only") for incremental changes. This is sustainable and scales to the velocity of a real engineering team.

---

## Integrating Threat Modeling Into Your SDLC

The biggest mistake: treating threat modeling as a pre-launch exercise.

![Security controls integrated throughout the software development lifecycle](/posts/os-weekly/images/security_leadership_funnel.png)

The right model:

```
Design review → Threat model session → Security requirements in tickets
      ↓
Development → Developers aware of specific threats for this feature
      ↓  
Code review → "Does this change address the threat we identified?"
      ↓
Testing → Specific test cases for identified threats (not just happy-path)
      ↓
Deployment → Confirm mitigations shipped; update threat model doc
```

**Practical checklist for your team:**

- [ ] Add "threat model" as a required step in your feature spec template
- [ ] Include threat model artifacts in your design doc template
- [ ] Tag JIRA/Linear tickets with the threat they address
- [ ] Review the threat model in your post-incident process: "Did we model this threat? Did we miss it? Why?"

---

## Tools: What You Actually Need

You don't need specialized tooling to start. A whiteboard beats most tools for the first year.

When you do want to formalize:

| Tool | Best for | Notes |
|------|----------|-------|
| **OWASP Threat Dragon** | Teams wanting an open-source, diagram-first tool | Free; integrates with git |
| **Microsoft Threat Modeling Tool** | Windows shops using Azure | Free; STRIDE-native |
| **draw.io / Miro** | Teams already using these for architecture diagrams | Low friction; no new tool to learn |
| **OWASP pytm** | Teams who want threat models as code | Python-based; generates diagrams from code |
| **IriusRisk** | Enterprise teams wanting automated threat generation | Paid; most powerful for complex systems |

Start with whatever lets you draw a DFD and list threats. Sophistication comes later.

---

## The Five Mistakes That Kill Threat Modeling Programs

**1. Doing it after the code is written.**
At that point you're doing a security review, not a threat model. You lose the ability to influence design decisions.

**2. Making it a security team exercise.**
Security teams can facilitate. They shouldn't own the output. Engineers own the system; engineers own the threat model.

**3. Boiling the ocean.**
You don't need to threat model every component on day one. Pick your highest-risk system — usually the one that touches customer PII or payment data — and start there.

**4. Producing documents, not work items.**
A threat model that lives in Confluence and produces no tickets is theater. Every threat that warrants mitigation should be a ticket before the session ends.

**5. Never revisiting it.**
A threat model from 18 months ago for a system that has been rewritten twice is actively harmful — it creates false confidence. Threat models are living documents. They rot if you don't maintain them.

---

## Key Takeaways

- Threat modeling is asking "how could this be attacked?" in a structured way — before an attacker does it for you.
- **STRIDE** (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) gives every engineer a lens to evaluate trust boundaries.
- A 60–90 minute session with four questions produces more security value than most security audits.
- The output should be tickets, not documents.
- Model when the attack surface changes — not just at launch.
- The biggest failure mode is making it a security team responsibility instead of an engineering practice.

---

> 💡 **If you found this helpful, learn the thinking that separates security engineers from security leaders.**
> ![CyberSHIELD PRO Membership](/posts/os-weekly/images/pro-os.png)
> 🚀 Supercharge your security career with 🔗 [CyberSHIELD PRO Membership](https://cybersecurityos.gumroad.com/l/cybershield-membership) — frameworks, deep-dives, and async Q&A every month.

---

## Further Reading

- [Adam Shostack, *Threat Modeling: Designing for Security*](https://www.wiley.com/en-us/Threat+Modeling%3A+Designing+for+Security-p-9781118809990) — the definitive book on the subject
- [OWASP Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [OWASP Threat Dragon](https://owasp.org/www-project-threat-dragon/) — open-source threat modeling tool
- [Microsoft SDL Threat Modeling](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling) — where STRIDE originated
- [NIST SP 800-154: Guide to Data-Centric System Threat Modeling](https://csrc.nist.gov/publications/detail/sp/800-154/draft)

---

Thanks for reading,

Michael

If you enjoy the content, then consider [buying me a coffee.](https://store.cybersecurityos.net/coffee)

---
