---
title: "Safety and Scope"
date: 2026-11-15
description: "What CyberSHIELD Labs do and don't do: local-only synthetic data, a loopback-only web lab, and teaching checks that are not production security controls."
draft: false
weight: 200
group: "REFERENCE"
---

## Safety and Scope

CyberSHIELD Labs are designed to be safe to run on a work or personal laptop, in a classroom, or in a team training session.

### Local and synthetic by default

- **Labs 1–8** work only on bundled or user-selected local files. They don't contact any network service, run payloads, or change system settings.
- **Lab 9** sends HTTP `HEAD` requests, but only to `localhost` or loopback IP addresses. It refuses remote hosts, ignores `HTTP(S)_PROXY` settings so requests never leave the machine, doesn't follow redirects, and uses short timeouts. Its `--demo` mode serves a temporary synthetic site on `127.0.0.1`.
- All fixtures are synthetic. Values such as `DEMO_API_KEY_...` are deliberately fake.
- The password lab reads input at a hidden prompt and never stores, logs, or sends it.

### Authorization

Only assess systems you own or are explicitly authorized to test. Don't modify the local-only safeguard in Lab 9 to point it at other systems.

### Teaching tools, not products

The checks are intentionally small and easy to read. That makes them good for learning and poor as production controls:

- They will miss real issues (false negatives) and flag harmless input (false positives).
- A clean result is not proof of safety or compliance.
- They don't replace organizational policy, current vendor documentation, maintained security tooling, or qualified review.

No lab claims to predict all threats or provide a complete compliance assessment.

### Data handling

Never put real credentials, personal data, customer data, or production configurations into a lab or its fixtures. If you extend a lab for your team, keep the same rule: synthetic data only.
