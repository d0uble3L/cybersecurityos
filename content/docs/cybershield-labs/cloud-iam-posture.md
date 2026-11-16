---
title: "Lab 2: Cloud IAM Posture"
date: 2026-11-15
description: "Hands-on lab: statically audit a provider-neutral cloud IAM policy for wildcard actions, wildcard resources, and public or unrestricted principals."
draft: false
weight: 20
group: "LABS"
lab: 2
navTitle: "Cloud IAM Posture"
focus: "Least privilege"
summary: "Audit a provider-neutral policy for wildcard actions, wildcard resources, and public principals."
run: "python3 -m labs.cloud_iam_posture.audit"
source: "labs/cloud_iam_posture"
---

## Lab 2: Cloud IAM Posture

**Why it matters:** Cloud identity is a high-impact control plane. One `"*"` in the wrong place can grant every action on every resource to anyone. Small policy mistakes are among the most common root causes of cloud exposure.

### What you'll practice

- Reading Allow statements the way an auditor does
- Recognizing full wildcards versus scoped grants
- Spotting public principals in string, list, and map forms

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.cloud_iam_posture.audit
```

`policy.json` has two statements. Statement 1 allows `*` actions on `*` resources for the principal `{"AWS": "*"}`. Statement 2 is a scoped grant: `storage:List` on `reports/monthly` for `role/report-reader`.

Example output:

```text
$ python3 -m labs.cloud_iam_posture.audit
[REVIEW] statement 1: wildcard_action — Allow statement grants every action.
[REVIEW] statement 1: wildcard_resource — Allow statement applies to every resource.
[REVIEW] statement 1: public_principal — Allow statement names a public or unrestricted principal.
```

### What the checks look for

| Check               | Triggers when                                                                                                                |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `wildcard_action`   | An Allow statement's actions include exactly `*`. Service-level wildcards such as `s3:*` are not reported as full wildcards. |
| `wildcard_resource` | An Allow statement's resources include exactly `*`.                                                                          |
| `public_principal`  | The principal is `*`, or a list or map containing `*` (for example `["*"]` or `{"AWS": ["*"]}`).                             |

### Try this

1. Narrow statement 1 in a copy of the policy to one action, one resource, and one named role. Rerun until the audit is clean.
2. Change an action to `storage:*`. It isn't flagged as a full wildcard. Is it still too broad? Sketch a rule that would catch it.
3. Add a `Deny` statement with `*`. Notice that it's ignored, and think about why Deny wildcards are often intentional.

### What this lab doesn't do

This small policy format doesn't model provider-specific conditions, inheritance, permission boundaries, resource policies, or every policy-language feature. A clean result is not proof of least privilege.

### Takeaway

Prefer scoped identities and least privilege, and verify effective permissions with your cloud provider's supported tooling before changing a real environment.

> **Source:** [labs/cloud_iam_posture](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/cloud_iam_posture) on GitHub.
