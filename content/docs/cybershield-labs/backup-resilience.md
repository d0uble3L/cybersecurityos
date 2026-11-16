---
title: "Lab 5: Backup Resilience"
date: 2026-11-15
description: "Hands-on lab: validate simulated backup evidence for encryption, immutability, and restore tests older than 90 days."
draft: false
weight: 50
group: "LABS"
lab: 5
navTitle: "Backup Resilience"
focus: "Recovery"
summary: "Validate simulated backup evidence for encryption, immutability, and recent restore tests."
run: "python3 -m labs.backup_resilience.validate"
source: "labs/backup_resilience"
---

## Lab 5: Backup Resilience

**Why it matters:** A backup only helps if you can recover from it. Ransomware crews target backups first, so encryption, deletion resistance (immutability), and practiced restores are core resilience controls.

### What you'll practice

- Treating restore tests as evidence, not assumptions
- Checking encryption and immutability claims
- Reasoning about evidence freshness

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.backup_resilience.validate
```

`backup_status.json` lists two simulated backups. `customer-records-demo` is encrypted but not immutable, and its last restore test was in October 2025. `analytics-demo` meets all checks (as of November 2026).

Example output:

```text
$ python3 -m labs.backup_resilience.validate
[REVIEW] customer-records-demo: immutability_not_confirmed
[REVIEW] customer-records-demo: restore_test_stale
```

### What the checks look for

| Check                        | Triggers when                                                        |
| ---------------------------- | -------------------------------------------------------------------- |
| `encryption_not_confirmed`   | `encrypted` is not true.                                             |
| `immutability_not_confirmed` | `immutable` is not true.                                             |
| `restore_test_missing`       | No restore-test date is recorded.                                    |
| `restore_test_stale`         | The last restore test is more than 90 days old.                      |
| `restore_test_in_future`     | The restore-test date is in the future, which suggests bad evidence. |

### Try this

1. Fix `customer-records-demo` in a copy of the fixture and rerun until it's clean.
2. Set a restore date in the future. Why is impossible evidence worth flagging?
3. Decide what restore-test interval fits a system you know, and what recovery time objective it should meet.

### What this lab doesn't do

The 90-day threshold is a teaching default, not a universal policy. The lab reads self-reported JSON and can't verify that a backup or restore actually worked.

### Takeaway

Test restores, measure recovery objectives, and protect backup administration separately from production access.

> **Source:** [labs/backup_resilience](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/backup_resilience) on GitHub.
