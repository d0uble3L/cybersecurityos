---
title: "Lab 4: Sensitive-Data Protection"
date: 2026-11-15
description: "Hands-on lab: detect and redact synthetic email addresses, phone numbers, and a fake API key pattern in local text before it is shared."
draft: false
weight: 40
group: "LABS"
lab: 4
navTitle: "Sensitive-Data Protection"
focus: "DLP & privacy"
summary: "Detect and redact email addresses, phone numbers, and a demo key pattern in local text."
run: "python3 -m labs.sensitive_data_protection.scan"
source: "labs/sensitive_data_protection"
---

## Lab 4: Sensitive-Data Protection

**Why it matters:** AI-assisted workflows and everyday data sharing make it easy for personal data or credentials to end up in logs, tickets, and prompts. Redacting before sharing is a simple control that prevents a lot of accidental exposure.

### What you'll practice

- Pattern-based detection of personal data
- Redacting values, not just reporting them
- Understanding false positives and false negatives in DLP

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.sensitive_data_protection.scan
```

`sample.txt` contains a synthetic contact line with an email address and phone number, plus a clearly fake `DEMO_API_KEY_...` placeholder.

Example output:

```text
$ python3 -m labs.sensitive_data_protection.scan
Found 3 pattern match(es).
[REVIEW] line 1: email
[REVIEW] line 1: phone
[REVIEW] line 2: demo_secret

Redacted preview:
Synthetic training contact: [REDACTED:email], [REDACTED:phone].
Placeholder only; do not use real credentials: [REDACTED:demo_secret]
```

### What the checks look for

| Check         | Triggers when                                                                        |
| ------------- | ------------------------------------------------------------------------------------ |
| `email`       | Email-like values.                                                                   |
| `phone`       | North American phone number formats such as `(555) 010-2020` or `555-010-2020`.      |
| `demo_secret` | The explicitly fake `DEMO_API_KEY_` pattern, standing in for a real secret detector. |

### Try this

1. Create a local text file with fake values in other formats, for example an international phone number. Which ones slip through?
2. Add a harmless line that looks like an email (`version@2.0.release`). Is it a false positive?
3. Sketch a pattern for another kind of identifier your team handles, and the false positives it might cause.

### What this lab doesn't do

The patterns are deliberately narrow. They will miss real sensitive data and can flag harmless text. Use only synthetic data in this lab, and don't treat it as a production DLP control.

### Takeaway

Minimize data collection, redact before sharing, and use approved secret-management and DLP systems for real information.

> **Source:** [labs/sensitive_data_protection](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/sensitive_data_protection) on GitHub.
