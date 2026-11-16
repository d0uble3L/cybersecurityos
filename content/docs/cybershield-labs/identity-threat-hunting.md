---
title: "Lab 3: Identity Threat Hunting"
date: 2026-11-15
description: "Hands-on lab: hunt synthetic authentication events for bursts of failed logins and repeated MFA denials that suggest password guessing or MFA push fatigue."
draft: false
weight: 30
group: "LABS"
lab: 3
navTitle: "Identity Threat Hunting"
focus: "Identity & MFA"
summary: "Hunt synthetic auth logs for login-failure bursts and MFA push-fatigue patterns."
run: "python3 -m labs.identity_threat_hunting.hunt"
source: "labs/identity_threat_hunting"
---

## Lab 3: Identity Threat Hunting

**Why it matters:** Identity systems sit in front of workforce, cloud, and service access. Authentication and MFA telemetry often shows an attack in progress, such as password spraying or MFA push fatigue, well before anything else does.

### What you'll practice

- Thinking in time windows rather than totals
- Recognizing password-guessing and push-fatigue patterns
- Weighing thresholds against false positives

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.identity_threat_hunting.hunt
```

`auth_events.json` contains five failed logins for `alex` within four minutes, followed by a success, and three denied MFA prompts for `sam` within two minutes.

Example output:

```text
$ python3 -m labs.identity_threat_hunting.hunt
[REVIEW] alex: repeated_login_failures (count=5)
[REVIEW] sam: repeated_mfa_denials (count=3)
```

### What the checks look for

| Check                     | Triggers when                                                                                      |
| ------------------------- | -------------------------------------------------------------------------------------------------- |
| `repeated_login_failures` | Five or more `login_failure` events for one user within 10 minutes.                                |
| `repeated_mfa_denials`    | Three or more `mfa_denied` events for one user within 10 minutes, a possible push-fatigue pattern. |

### Try this

1. Spread `sam`'s MFA denials an hour apart in a copy of the fixture. The finding disappears. Is that the right call?
2. Notice that `alex` succeeded right after five failures. Write down why a failure burst followed by a success deserves more attention than failures alone.
3. Add events for a second user from the same burst window. How would you detect one source spraying many accounts?

### What this lab doesn't do

Thresholds are teaching defaults. The lab ignores source IPs, devices, geography, and user baselines, and it doesn't lock accounts or respond automatically. Real alerts need context and will have false positives.

### Takeaway

Correlate identity events with organizational context, protect authentication logs, and investigate suspicious activity through your approved incident process.

> **Source:** [labs/identity_threat_hunting](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/identity_threat_hunting) on GitHub.
