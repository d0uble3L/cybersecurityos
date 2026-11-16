---
title: "Lab 6: Software Supply Chain"
date: 2026-11-15
description: "Hands-on lab: check a local SBOM-like inventory for components without exact versions, valid SHA-256 digests, or a recorded source."
draft: false
weight: 60
group: "LABS"
lab: 6
navTitle: "Software Supply Chain"
focus: "SBOM quality"
summary: "Check an SBOM-like inventory for unpinned versions, missing digests, and unknown sources."
run: "python3 -m labs.software_supply_chain.audit"
source: "labs/software_supply_chain"
---

## Lab 6: Software Supply Chain

**Why it matters:** Teams need to know what they build and ship, where each component came from, and how to verify it. Recent package-registry worms and compromised build tools show that an incomplete inventory slows down every incident response.

### What you'll practice

- Reading an SBOM for completeness
- Why exact versions and digests matter
- Tracking component provenance

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.software_supply_chain.audit
```

`sbom.json` lists two components. `demo-parser` is pinned to `1.4.2` with a placeholder-shaped SHA-256 digest and a source. `demo-helper` uses the version `latest`, has no digest, and records only a vague source.

Example output:

```text
$ python3 -m labs.software_supply_chain.audit
[REVIEW] demo-helper: version_not_exact
[REVIEW] demo-helper: hash_missing_or_invalid
```

### What the checks look for

| Check                     | Triggers when                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------- |
| `version_not_exact`       | The version isn't an exact `major.minor.patch` (with optional pre-release or build suffix). |
| `hash_missing_or_invalid` | There's no well-formed 64-character SHA-256 digest.                                         |
| `source_missing`          | No source is recorded.                                                                      |
| `empty_inventory`         | The SBOM has no components at all.                                                          |

### Try this

1. Pin `demo-helper` to an exact version, add a 64-character hex digest, and rerun in a copy of the file.
2. Set a version to `null`. The audit reports it as missing instead of crashing. Why does robust parsing matter for security tooling?
3. Look at a real SBOM from one of your builds (CycloneDX or SPDX) and list the fields this lab doesn't check.

### What this lab doesn't do

Digests here are placeholder-shaped teaching data, not evidence that an artifact is authentic. The lab doesn't download packages, query vulnerability databases, or claim that any component is vulnerable.

### Takeaway

Generate SBOMs from your build systems, verify provenance and artifact digests through trusted pipelines, and keep dependency risk data current.

> **Source:** [labs/software_supply_chain](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/software_supply_chain) on GitHub.
