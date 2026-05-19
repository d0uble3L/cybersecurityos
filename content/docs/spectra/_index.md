---
title: "SPECTRA"
date: 2026-05-18
description: "SPECTRA — Security Platform for Expert-level Correlation, Triage, and Risk Analysis. Official open-source documentation."
draft: false
---

## SPECTRA Documentation

**SPECTRA** — _Security Platform for Expert-level Correlation, Triage, and Risk Analysis_ — is an open-source, AI-powered CLI that transforms raw scanner output into ranked findings, attack chain analysis, and executive summaries. Powered by [Claude](https://www.anthropic.com/).

> **License:** Apache License 2.0
> **Source:** [github.com/d0uble3L/spectra](https://github.com/d0uble3L/spectra)
> **Status:** Beta — active development

---

### Documentation Sections

| Section | Description |
|---|---|
| [Installation](/docs/spectra/installation/) | System requirements and install methods |
| [Quick Start](/docs/spectra/quickstart/) | Run your first analysis in under 5 minutes |
| [CLI Reference](/docs/spectra/cli-reference/) | Full command and flag reference |
| [Configuration](/docs/spectra/configuration/) | Environment variables and `.env` setup |
| [Supported Scanners](/docs/spectra/scanners/) | Trivy, Semgrep, Nessus, Burp Suite, and generic input |
| [Output Formats](/docs/spectra/output-formats/) | Markdown, JSON, and report structure |
| [CI/CD Integration](/docs/spectra/cicd-integration/) | GitHub Actions, GitLab CI, Jenkins |
| [Architecture](/docs/spectra/architecture/) | Design decisions, prompt caching, and AI layer |
| [Contributing](/docs/spectra/contributing/) | How to contribute, report issues, and request features |
| [License](/docs/spectra/license/) | Apache 2.0 license, copyright, and trademark notice |

---

### What SPECTRA Does

SPECTRA sits **downstream** of your existing security scanners. It does not replace them — it makes them actionable at scale.

A single command:

```bash
spectra analyze trivy.json --format both --output reports/run1
```

Produces:

- **Ranked findings** — calibrated by real-world risk, not raw CVSS scores
- **Attack chain analysis** — connecting related vulnerabilities into exploitable paths  
- **Executive summaries** — plain-language overviews ready for leadership or GRC audits
- **Remediation guidance** — specific, contextual steps — not generic patch instructions

---

### Supported Scanners

| Scanner | Input Format | Detection |
|---|---|:---:|
| [Trivy](https://trivy.dev/) | JSON | Automatic |
| [Semgrep](https://semgrep.dev/) | JSON | Automatic |
| Nessus / OpenVAS | Text | `--scanner generic` |
| Burp Suite | Text export | `--scanner generic` |
| Pentest notes | Plain text | `--scanner generic` |

---

### Copyright Notice

Copyright © 2026 CybersecurityOS. All rights reserved.

SPECTRA is distributed under the [Apache License 2.0](/docs/spectra/license/). The name "SPECTRA" and the CybersecurityOS wordmark are trademarks of CybersecurityOS and may not be used without prior written permission except as permitted by applicable trademark law.
