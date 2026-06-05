---
title: "Output Formats"
date: 2026-05-18
description: "SPECTRA output formats — Markdown, JSON, and report structure reference."
draft: false
weight: 60
---

## Output Formats

SPECTRA produces two output formats: **Markdown** (human-readable) and **JSON** (machine-readable for downstream tooling). Use `--format both` to generate both simultaneously.

---

### Format Options

| Value                | Files Generated                 | Use Case                                           |
| -------------------- | ------------------------------- | -------------------------------------------------- |
| `markdown` (default) | `<output>.md`                   | Reports, executive briefings, GRC documentation    |
| `json`               | `<output>.json`                 | SIEM integration, ticketing automation, dashboards |
| `both`               | `<output>.md` + `<output>.json` | Full reporting + downstream automation             |

```bash
spectra analyze trivy.json --format both --output reports/scan-2026-05-18
# Writes: reports/scan-2026-05-18.md
#         reports/scan-2026-05-18.json
```

---

### Markdown Report Structure

The Markdown report is structured for human review and is suitable for sharing directly with engineers, analysts, and leadership.

```
# SPECTRA Analysis Report
Generated: 2026-05-18T14:32:00Z
Scanner: Trivy | Findings: 47 | Critical: 3 | High: 11

---

## Executive Summary

[Plain-language summary of the scan results, overall risk posture,
and recommended priority actions. Written for non-technical leadership.]

---

## Attack Chain Analysis

[AI-identified vulnerability chains that form exploitable paths.
Each chain lists the constituent CVEs/findings, the attack vector,
and the resulting blast radius if exploited.]

### Chain 1: Container Escape via CVE-2024-XXXX → Lateral Movement

...

---

## Ranked Findings

### Critical

#### CVE-2024-XXXX — [Package Name] [Version]
- **CVSS Score:** 9.8
- **EPSS Probability:** 0.87 (87th percentile)
- **Context:** Exploitable without authentication via network vector
- **Remediation:** Upgrade [package] to [fixed-version]

...

### High

...

---

## Remediation Summary

| Finding | Severity | Remediation | Effort |
|---|---|---|---|
| CVE-XXXX | Critical | Upgrade package X to 1.2.3 | Low |
...
```

---

### JSON Report Structure

The JSON report exposes all analysis fields in a structured format for integration with ticketing systems, SIEM platforms, and dashboards.

```json
{
  "meta": {
    "spectra_version": "0.x",
    "generated_at": "2026-05-18T14:32:00Z",
    "scanner": "trivy",
    "model": "claude-sonnet-4-6",
    "total_findings": 47
  },
  "executive_summary": "...",
  "attack_chains": [
    {
      "id": "chain-1",
      "title": "Container Escape via CVE-2024-XXXX",
      "severity": "critical",
      "cve_ids": ["CVE-2024-XXXX", "CVE-2024-YYYY"],
      "description": "...",
      "blast_radius": "..."
    }
  ],
  "findings": [
    {
      "id": "CVE-2024-XXXX",
      "package": "package-name",
      "installed_version": "1.0.0",
      "fixed_version": "1.2.3",
      "severity": "critical",
      "cvss_score": 9.8,
      "epss_probability": 0.87,
      "ai_risk_context": "...",
      "remediation": "...",
      "attack_chain_ids": ["chain-1"]
    }
  ],
  "token_usage": {
    "input_tokens": 12450,
    "output_tokens": 1820,
    "cache_read_tokens": 9800,
    "cache_write_tokens": 2650
  }
}
```

---

### Integrating JSON Output

**Jira (example):**

```bash
# Parse critical findings and create tickets
spectra analyze trivy.json --format json --output /tmp/report
jq '.findings[] | select(.severity == "critical")' /tmp/report.json \
  | your-jira-ticket-script
```

**Slack notification:**

```bash
SUMMARY=$(jq -r '.executive_summary' /tmp/report.json)
curl -X POST $SLACK_WEBHOOK -d "{\"text\": \"SPECTRA scan complete:\\n${SUMMARY}\"}"
```

**SIEM ingest:**

The JSON structure is flat enough to ingest directly into Elasticsearch, Splunk, or any SIEM that accepts structured JSON. Map `findings[].id` as the document ID to support deduplication across scan runs.

---

**Next:** [CI/CD Integration →](/docs/spectra/cicd-integration/)
