---
title: "Quick Start"
date: 2026-05-18
description: "Run your first SPECTRA analysis in under 5 minutes."
draft: false
weight: 20
---

## Quick Start

This guide gets you from install to your first analysis in under 5 minutes. It assumes you have already completed [Installation](/docs/spectra/installation/).

---

### Step 1 — Set your API key

```bash
cp .env.example .env
# Edit .env and add your Anthropic API key
ANTHROPIC_API_KEY=sk-ant-...
```

---

### Step 2 — Run your first analysis

Use the bundled Trivy sample to confirm the setup:

```bash
spectra analyze tests/samples/trivy_sample.json
```

SPECTRA auto-detects the scanner type from the file structure and outputs a ranked summary to stdout.

---

### Step 3 — Analyze your own scanner output

**Trivy (container or filesystem scan):**

```bash
# Generate a Trivy scan first
trivy image your-image:latest -f json -o trivy.json

# Analyze with SPECTRA
spectra analyze trivy.json --format both --output reports/run1
```

**Semgrep (SAST):**

```bash
# Generate a Semgrep scan
semgrep --config=auto --json > semgrep.json

# Pipe directly into SPECTRA
cat semgrep.json | spectra analyze --scanner semgrep --format json --output reports/pr-check
```

**Generic / pentest notes / Nessus:**

```bash
spectra analyze nessus_export.txt --scanner generic --format markdown --output reports/pentest
```

---

### Step 4 — Review outputs

By default, SPECTRA writes reports to the path specified in `--output`:

```
reports/run1.md      ← Human-readable ranked summary
reports/run1.json    ← Structured JSON for downstream tooling
```

Open the Markdown report to see:

- **Executive summary** — plain-language overview for leadership
- **Ranked findings** — severity-ordered with contextual risk notes
- **Attack chains** — connected vulnerability paths
- **Remediation steps** — specific guidance per finding

---

### Common Options at a Glance

| Flag        | Description                                       | Example                  |
| ----------- | ------------------------------------------------- | ------------------------ |
| `--format`  | Output format: `markdown`, `json`, or `both`      | `--format both`          |
| `--output`  | Output file path (no extension needed)            | `--output reports/scan1` |
| `--scanner` | Force scanner type: `trivy`, `semgrep`, `generic` | `--scanner generic`      |
| `--usage`   | Print token usage stats after analysis            | `--usage`                |

---

### Next Steps

- [CLI Reference](/docs/spectra/cli-reference/) — full flag and command reference
- [Configuration](/docs/spectra/configuration/) — tune model behavior and output defaults
- [CI/CD Integration](/docs/spectra/cicd-integration/) — add SPECTRA to your pipeline

---

**Next:** [CLI Reference →](/docs/spectra/cli-reference/)
