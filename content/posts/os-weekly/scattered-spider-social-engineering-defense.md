---
title: "Scattered Spider's Playbook Is Simple. Your Defense Needs to Be Simpler."
date: 2026-07-04
draft: false
author: "Michael Tayo"
tags:
  [
    "CybersecurityOS",
    "Cybersecurity",
    "Social Engineering",
    "Scattered Spider",
    "Threat Intelligence",
    "MFA",
    "Security Leadership",
    "Identity Security",
  ]
categories: ["Leadership", "Threat Intelligence"]
description: "Two teenagers caused £29M in damage at TfL without writing a single exploit. The Scattered Spider case is a masterclass in what social engineering looks like at scale — and what defending against it actually requires."
slug: "scattered-spider-social-engineering-defense"
show_reading_time: true
images:
  - /posts/os-weekly/images/scattered-spider-defense-hero.svg
featured_image: /posts/os-weekly/images/scattered-spider-defense-thumb.svg
keywords: "scattered spider, social engineering defense, vishing, help desk security, phishing resistant MFA, FIDO2, security leadership, Transport for London hack"
---

On June 22, 2026, Thalha Jubair, 20, and Owen Flowers, 18 — both from the UK — walked into Woolwich Crown Court and pleaded guilty on day one of a trial that had been set to run six weeks. Their target: Transport for London. Their tool: a phone call.

![Scattered Spider's Playbook Is Simple. Your Defense Needs to Be Simpler.](/posts/os-weekly/images/scattered-spider-defense-hero.svg)

The attack, which ran August 31–September 3, 2024, exposed the personal data of an estimated 10 million people — names, email addresses, mobile numbers, and physical addresses — and forced all 28,000 TfL employees to travel to a TfL office in person to reset their passwords. Total losses and recovery costs: £29 million. Sentencing is scheduled for July 15, 2026.

Jubair and Flowers are two members of a broader operation that, according to federal investigators, conducted 120 intrusions against 47 U.S. entities between May 2022 and September 2025, collecting at least $115 million in ransom. The group is known by a rotating roster of names — Scattered Spider, UNC3944, Octo Tempest, Muddled Libra, Oktapus — but the playbook has remained remarkably consistent throughout.

They didn't break encryption. They didn't find a zero-day. They made phone calls.

## Who Scattered Spider Is — and Why That Matters

Most threat actors that cause $115 million in damage are nation-state groups with substantial resources, cover identities, and years of operational security discipline. Scattered Spider is none of those things. The group is largely composed of native English-speaking teenagers and young adults from the US and UK who coordinate through Telegram and Discord.

That framing is uncomfortable for security leaders, because it removes the convenient narrative that you were outmatched by a sophisticated adversary. The TfL attack was not a sophisticated nation-state operation. It was two teenagers with a phone, a LinkedIn account, and a clear understanding of how corporate IT processes work.

Their edge is not technical capability. It is psychological fluency. They know how help desks are trained, how employees are conditioned to comply with authority, and how organizations have built convenience into their identity verification workflows in ways that create exploitable gaps.

## The Three-Part Playbook

Understanding Scattered Spider's attack chain is the first step toward defending against it. The playbook is consistent enough that CISA and the FBI have issued multiple joint advisories documenting it in detail.

**Step 1: OSINT and Persona Construction**

The attack starts on LinkedIn. Threat actors identify target employees — often someone in IT or with broad system access — and build a convincing impersonation profile using publicly available information: job title, manager's name, office location, internal terminology. In the MGM Resorts attack, Scattered Spider placed a single help desk call lasting approximately ten minutes, armed with nothing more than a LinkedIn profile.

**Step 2: Help Desk Exploitation**

Once the persona is ready, the attacker calls IT support and impersonates the employee. The goal is straightforward: get the help desk to reset the password, register a new MFA device, or grant remote access. Help desks are operationally incentivized to resolve tickets quickly. That pressure creates exactly the kind of friction-free environment this attack requires.

What makes this particularly effective is that most help desk verification processes were designed to confirm identity for legitimate forgotten-password scenarios — not to withstand a determined social engineer with a researched backstory. A date of birth, an employee ID, and a plausible story will pass the bar at most organizations.

**Step 3: MFA Bypass**

If MFA is in the path, Scattered Spider has three reliable routes around it. The first is push bombing — sending continuous MFA push notifications until the user approves out of fatigue or confusion. The second is SIM swapping: using a separate social engineering operation against a mobile carrier to redirect the victim's phone number to an attacker-controlled device, capturing SMS-based OTP codes in transit. The TfL attack used SIM swapping via a Telegram channel called Star Chat to intercept employee credentials and authentication codes.

The third route is the simplest: ask the help desk to register a new MFA device. If the help desk can reset a password and add a new authenticator in the same call without additional verification, MFA provides no barrier at all.

## Why Organizations Keep Failing

The reason this playbook keeps working is structural. Most enterprise security programs are built around technological controls — firewalls, EDR, SIEM — and treat human processes as peripheral. The help desk is an operational function, not a security function, and it is rarely subject to the same security design rigor as the systems it provides access to.

Standard SMS and push-based MFA was never designed to defend against a caller who can intercept codes or convince your support team to bypass them. These controls raise the cost of opportunistic attacks, but Scattered Spider is not opportunistic — they are methodical, patient, and rehearsed.

The gap is not technology. It is process.

## The Defensive Framework

The good news is that the mitigations are well-documented, and none of them require cutting-edge tooling. CISA, the FBI, and international partners have been explicit about what works.

**1. Move to phishing-resistant MFA**

SMS and push notification MFA cannot stop SIM swapping or push bombing. FIDO2/WebAuthn hardware keys (YubiKey and equivalents) and passkeys are resistant to both — there is no code to intercept and no push to approve. If your organization is still on SMS-based OTP or authenticator push for high-risk accounts, Scattered Spider's primary bypass routes are open.

Start with your highest-risk accounts: privileged admin access, IT support staff, HR, and finance. These are the accounts most valuable to an attacker who's just cleared your help desk.

**2. Formalize help desk identity verification**

A help desk worker who cannot definitively verify a caller's identity should not be able to reset credentials or register MFA devices. That sounds obvious, but most organizations have no formal policy encoding it.

Build a protocol with teeth: callbacks to verified numbers on file (not numbers the caller provides), manager co-authorization for MFA device changes, and a hard rule that password reset and MFA device registration cannot happen in the same call without a second verification factor. The inconvenience is manageable. The alternative is TfL.

**3. Separate password reset and MFA device registration**

This is the single highest-leverage process change most organizations can make. An attacker who can reset both in one call has effectively bypassed your entire identity stack in a single support interaction. Requiring two independent verification events — or out-of-band confirmation from a manager — for MFA device registration breaks the attack chain.

**4. Red team your help desk**

You will not know whether your verification process holds until someone tests it. Run quarterly vishing exercises against your own support teams. The results will be clarifying. Organizations that do this consistently discover gaps they would not have found any other way, and they develop muscle memory in their support staff that makes the real attacks easier to catch.

## Getting Started

If you take one thing from this post, let it be a focused audit of four things:

1. **What can your help desk do with a single phone call?** Map every action available to a caller who passes your current verification — password reset, MFA registration, remote access grants, account unlocks.
2. **Which accounts are most valuable to an attacker?** Prioritize FIDO2 rollout for those accounts first. You do not need organization-wide deployment to close the highest-risk gaps.
3. **Does your help desk have a documented verification protocol?** If the answer is "it depends on the agent," you have a policy problem that technology cannot solve.
4. **When did you last test it?** Schedule a vishing exercise. The cost of running it is almost nothing compared to what you will learn.

## The Uncomfortable Conclusion

Two teenagers from the UK cost Transport for London £29 million and exposed the personal data of 10 million commuters. They did not exploit a sophisticated vulnerability or defeat a well-designed security control. They walked through a door that most organizations have left open because the process of securing it is operationally inconvenient.

The technology to defend against this exists and is affordable. Phishing-resistant MFA, formal help desk verification protocols, and regular red team exercises are not exotic capabilities. What they require is the organizational will to prioritize process discipline alongside technical controls — and the recognition that the most dangerous vulnerability in many environments right now is a 10-minute phone call.

---

**References**

- [Scattered Spider Hackers Plead Guilty on Day 1 of Trial](https://krebsonsecurity.com/2026/06/scattered-spider-hackers-plead-guilty-on-day-1-of-trial/) — Krebs on Security
- [Scattered Spider Members Plead Guilty to Hacking Transport for London](https://www.bleepingcomputer.com/news/security/scattered-spider-members-plead-guilty-to-hacking-transport-for-london/) — BleepingComputer
- [CISA and Partners Release Updated Advisory on Scattered Spider Group](https://www.cisa.gov/news-events/alerts/2025/07/29/cisa-and-partners-release-updated-advisory-scattered-spider-group) — CISA
- [Two Scattered Spider Members Plead Guilty Over TfL Cyberattack](https://www.helpnetsecurity.com/2026/06/23/transport-london-cyberattack-scattered-spider-members-plead-guilty/) — Help Net Security

---

> 💡 **Want to lead with clarity in the AI era?**
> If you're building a security program, managing a team, or trying to make the leap from practitioner to leader, this is the resource you've been missing:
> 🔗 [Cybersecurity Leadership OS: Battle-Tested Mental Models for Clarity, Speed & Command](https://store.cybersecurityos.net/l/cybersecurity-leadership-os)

Follow CyberShield for more weekly breakdowns built for the next generation of security professionals.
