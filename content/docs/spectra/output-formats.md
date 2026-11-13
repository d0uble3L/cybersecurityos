---
title: "Output Formats"
date: 2026-05-18
description: "SPECTRA output formats — Markdown, JSON, and report structure reference."
draft: false
weight: 60
---

## Output Formats

SPECTRA produces two output formats: **Markdown** (human-readable) and **JSON** (machine-readable for downstream tooling). Both are rendered from the same analysis, so they always contain the same findings. Use `--format both` to generate both from a single API call.

---

### Format Options

| Value                | With `--output <path>`      | Without `--output`           | Use case                                           |
| -------------------- | --------------------------- | ---------------------------- | -------------------------------------------------- |
| `markdown` (default) | `<path>.md`                 | Rendered to the terminal     | Reports, executive briefings, GRC documentation    |
| `json`               | `<path>.json`               | Printed to stdout            | SIEM integration, ticketing automation, dashboards |
| `both`               | `<path>.md` + `<path>.json` | Both printed, Markdown first | Full reporting plus downstream automation          |

```bash
spectra analyze trivy.json --format both --output reports/scan-2026-05-18
# Writes: reports/scan-2026-05-18.md
#         reports/scan-2026-05-18.json
```

SPECTRA replaces any extension on the `--output` path, so `--output reports/scan.txt` writes `reports/scan.md`. Missing parent directories are created.

Status messages, the progress spinner and `--usage` token stats go to stderr, so `--format json` without `--output` is safe to pipe:

```bash
spectra analyze trivy.json --format json | jq '.overall_risk_score'
```

---

### Markdown Report Structure

The Markdown report is ordered for a reader who has 30 seconds, then for the engineer who has to fix things. Sections with no content (for example, no attack chains) are left out.

```text
# SPECTRA — Security Intelligence Report
*Analyzed: 2026-11-13 02:16 UTC | Scanner: Semgrep*

---

## ⚠️ Overall Risk: HIGH &nbsp; `84/100`

### Executive Summary

This SAST scan found six code-level weaknesses (no critical, three high, three
medium), led by a string-concatenated SQL query in app/api/users.py ...

### Findings at a Glance

| Severity | Count |
|:---------|------:|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 3 |
| 🟡 MEDIUM | 3 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |

*6 raw findings → 6 unique after dedup*

---

## ⚡ Immediate Actions Required

1. Fix SPEC-001: parameterize the query in app/api/users.py:47 ...
2. Fix SPEC-002: roll the Stripe secret key in the Stripe Dashboard now ...

## 🔗 Attack Chains

### SQL injection to mass account takeover
*Chained findings: `SPEC-001`, `SPEC-004`*

1) The attacker exploits the concatenated query in users.py (SPEC-001) ...

## Detailed Findings

### 🟠 HIGH — SQL injection via string concatenation in cursor.execute (users.py:47) &nbsp; `SPEC-001`

**Component:** `app/api/users.py:47`  |  **Exploitability:** Requires Access

The `username` value is concatenated directly into a raw SQL string ...

> **Remediation:** Replace the concatenated query with a parameterized one ...

**References:**
- https://cwe.mitre.org/data/definitions/89.html

## Strategic Recommendations

- Gate CI on Semgrep (including the Django, Python and secrets rulesets) ...
```

| Section                    | Contents                                                                                                                             |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Header and overall risk    | Analysis time, detected scanner, overall risk level and 0–100 score                                                                  |
| Executive Summary          | Two to three sentences for leadership: overall risk, top threat, recommended focus                                                   |
| Findings at a Glance       | Count per severity, plus raw versus de-duplicated finding counts                                                                     |
| Immediate Actions Required | The top-priority fixes, referencing `SPEC-NNN` finding IDs                                                                           |
| Attack Chains              | Findings that combine into a higher-order risk, with the attack steps                                                                |
| Detailed Findings          | Every finding, sorted by severity: component, CVSS (when known), exploitability, description, remediation, and up to five references |
| Strategic Recommendations  | Longer-term process, tooling or architecture improvements                                                                            |

---

### JSON Report Structure

The JSON report is the full analysis as a single object. Field names and enum values are stable, so it is safe to build automation on. Example, trimmed to two findings:

```json
{
  "executive_summary": "This SAST scan found six code-level weaknesses (no critical, three high, three medium), led by a string-concatenated SQL query in app/api/users.py ...",
  "overall_risk_score": 84,
  "risk_level": "HIGH",
  "scanner_type": "Semgrep",
  "scan_metadata": {
    "total_raw_findings": 6,
    "after_dedup": 6,
    "scan_timestamp": null
  },
  "findings_by_severity": {
    "CRITICAL": 0,
    "HIGH": 3,
    "MEDIUM": 3,
    "LOW": 0,
    "INFO": 0
  },
  "findings": [
    {
      "id": "SPEC-001",
      "severity": "HIGH",
      "title": "SQL injection via string concatenation in cursor.execute (users.py:47)",
      "description": "The `username` value is concatenated directly into a raw SQL string passed to `cursor.execute` ...",
      "affected_component": "app/api/users.py:47",
      "cvss_score": null,
      "exploitability": "Requires Access",
      "remediation": "Replace the concatenated query with a parameterized one ...",
      "references": [
        "https://cwe.mitre.org/data/definitions/89.html",
        "https://owasp.org/Top10/A03_2021-Injection/"
      ]
    },
    {
      "id": "SPEC-002",
      "severity": "HIGH",
      "title": "Hardcoded Stripe secret key committed in settings.py",
      "description": "`STRIPE_SECRET_KEY` is hardcoded in app/config/settings.py ...",
      "affected_component": "app/config/settings.py:42",
      "cvss_score": null,
      "exploitability": "Requires Access",
      "remediation": "1) Roll/revoke the key in the Stripe Dashboard now ...",
      "references": ["https://cwe.mitre.org/data/definitions/798.html"]
    }
  ],
  "attack_chains": [
    {
      "name": "SQL injection to mass account takeover",
      "description": "1) The attacker exploits the concatenated query in users.py (SPEC-001) ...",
      "finding_ids": ["SPEC-001", "SPEC-004"]
    }
  ],
  "immediate_actions": [
    "Fix SPEC-001: parameterize the query in app/api/users.py:47 ...",
    "Fix SPEC-002: roll the Stripe secret key in the Stripe Dashboard now ..."
  ],
  "strategic_recommendations": [
    "Gate CI on Semgrep (including the Django, Python and secrets rulesets) ..."
  ],
  "analyzed_at": "2026-11-13T02:16:07.981410Z"
}
```

#### Top-level fields

| Field                       | Type             | Description                                                                                                       |
| --------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------- |
| `executive_summary`         | string           | Two to three sentences for leadership                                                                             |
| `overall_risk_score`        | integer 0–100    | Worst realistic outcome if left unmitigated. `0` when the input contains no findings                              |
| `risk_level`                | enum             | `CRITICAL`, `HIGH`, `MEDIUM`, or `LOW`                                                                            |
| `scanner_type`              | string           | Detected scanner, e.g. `Trivy`, `Semgrep`, `Nessus`, `Mixed`, `Generic`                                           |
| `scan_metadata`             | object           | `total_raw_findings` (before de-duplication), `after_dedup`, `scan_timestamp` (ISO 8601 from the scan, or `null`) |
| `findings_by_severity`      | object           | Count per severity: `CRITICAL`, `HIGH`, `MEDIUM`, `LOW`, `INFO`                                                   |
| `findings`                  | array            | Ranked findings, most severe first (see below)                                                                    |
| `attack_chains`             | array            | Each has `name`, `description`, and `finding_ids` (two or more `SPEC-NNN` IDs). Empty when no real chain exists   |
| `immediate_actions`         | array of strings | Top-priority fixes                                                                                                |
| `strategic_recommendations` | array of strings | Longer-term improvements                                                                                          |
| `analyzed_at`               | string           | ISO 8601 UTC time the report was produced                                                                         |

#### Finding fields

| Field                | Type           | Description                                                                                                                  |
| -------------------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `id`                 | string         | `SPEC-001`, `SPEC-002`, … numbered within this report only                                                                   |
| `severity`           | enum           | `CRITICAL`, `HIGH`, `MEDIUM`, `LOW`, or `INFO` (uppercase). SPECTRA's contextual rating, which can differ from the scanner's |
| `title`              | string         | Short, scannable title                                                                                                       |
| `description`        | string         | What the issue is, why it matters, and what an attacker could do                                                             |
| `affected_component` | string         | Package and version, file path and line, service, or host                                                                    |
| `cvss_score`         | number or null | CVSS score when the scanner provides one; `null` otherwise (SPECTRA does not invent scores)                                  |
| `exploitability`     | enum           | `Immediate`, `Requires Access`, `Complex`, or `Theoretical`                                                                  |
| `remediation`        | string         | The specific fix: version to upgrade to, setting to change, or code pattern to apply                                         |
| `references`         | array          | CVE IDs and URLs (advisories, CWE entries)                                                                                   |

Token usage is not part of the report. Use `--usage` to print it to stderr.

---

### Integrating JSON Output

Severity values are uppercase, so filter on `"CRITICAL"`, not `"critical"`.

**Fail a CI job on critical findings:**

```bash
spectra analyze trivy.json --format json --output reports/scan
jq -e '.findings_by_severity.CRITICAL == 0' reports/scan.json > /dev/null \
  || { echo "SPECTRA found critical findings"; exit 1; }
```

**Jira (example):**

```bash
# One ticket payload per critical or high finding
jq -c '.findings[] | select(.severity == "CRITICAL" or .severity == "HIGH")
       | {summary: "\(.id) \(.title)", component: .affected_component, description: .remediation}' \
  reports/scan.json | your-jira-ticket-script
```

**Slack notification:**

```bash
jq -n --arg text "SPECTRA: $(jq -r '"\(.risk_level) (\(.overall_risk_score)/100) — \(.executive_summary)"' reports/scan.json)" \
  '{text: $text}' | curl -sS -X POST -H 'Content-Type: application/json' -d @- "$SLACK_WEBHOOK"
```

Building the payload with `jq` keeps quotes and newlines in the summary from breaking the JSON.

**SIEM ingest:**

Ingest one document per element of `findings[]`, and copy `analyzed_at`, `scanner_type` and `risk_level` onto each so they can be filtered. Don't use `findings[].id` as a document ID: `SPEC-NNN` numbers restart in every report. To de-duplicate across scan runs, key on `affected_component` plus the CVE IDs in `references`. Both come from the scanner's data. `title` and `description` are written by the model and vary between runs.

---

**Next:** [CI/CD Integration →](/docs/spectra/cicd-integration/)
