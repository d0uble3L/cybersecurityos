---
title: "Supported Scanners"
date: 2026-05-18
description: "Scanner input formats supported by SPECTRA — Trivy, Semgrep, Nessus, Burp Suite, and generic text."
draft: false
weight: 50
---

## Supported Scanners

SPECTRA processes output from the following scanner types. Auto-detection is attempted by default; use `--scanner` to force a specific parser when auto-detection does not apply.

---

### Trivy

[Trivy](https://trivy.dev/) is an open-source vulnerability scanner for containers, filesystems, Git repositories, and cloud infrastructure.

**Auto-detected:** Yes — from JSON structure  
**Input format:** JSON (`-f json`)

**Generate scan output:**

```bash
# Container image scan
trivy image your-image:latest -f json -o trivy.json

# Filesystem scan
trivy fs . -f json -o trivy-fs.json

# Kubernetes scan
trivy k8s --report summary -f json -o trivy-k8s.json
```

**Analyze with SPECTRA:**

```bash
spectra analyze trivy.json --format both --output reports/container-scan
```

**What SPECTRA extracts from Trivy output:**

- CVE identifiers, CVSS scores, and severity ratings
- Affected package names and installed versions
- Fixed versions (when available)
- Vulnerability descriptions for AI contextualization

---

### Semgrep

[Semgrep](https://semgrep.dev/) is a static analysis tool for finding bugs, security vulnerabilities, and code quality issues.

**Auto-detected:** Yes — from JSON structure  
**Input format:** JSON (`--json`)

**Generate scan output:**

```bash
# Run with default security ruleset
semgrep --config=auto --json > semgrep.json

# Run with a specific ruleset
semgrep --config=p/owasp-top-ten --json > semgrep-owasp.json
```

**Analyze with SPECTRA:**

```bash
# From file
spectra analyze semgrep.json --format both --output reports/sast-scan

# From stdin (useful in CI pipelines)
semgrep --config=auto --json | spectra analyze --scanner semgrep --format json --output reports/pr-check
```

**What SPECTRA extracts from Semgrep output:**

- Rule IDs and CWE mappings
- File paths and line numbers for each finding
- Severity levels and confidence ratings
- Code snippets for contextual analysis

---

### Generic / Nessus / OpenVAS / Burp Suite

The generic scanner mode accepts unstructured text-based output from tools that do not export a parseable JSON format, including:

- [Nessus](https://www.tenable.com/products/nessus) exports
- [OpenVAS](https://www.openvas.org/) reports
- [Burp Suite](https://portswigger.net/burp) HTML or text exports
- Pentest notes and manual findings

**Auto-detected:** No — must specify `--scanner generic`  
**Input format:** Plain text or HTML

**Analyze with SPECTRA:**

```bash
# Nessus text export
spectra analyze nessus_report.txt --scanner generic --format both --output reports/nessus

# Burp Suite export
spectra analyze burp_export.txt --scanner generic --format markdown --output reports/burp

# Pentest notes
spectra analyze pentest_notes.txt --scanner generic --format both --output reports/engagement-1
```

**Guidance for generic input quality:**

The AI analysis quality scales with input structure. For best results with generic input:

- Include CVE identifiers where available
- Note host/IP context for each finding
- List affected service or component names
- Include severity ratings from the source tool

---

### Adding New Scanners

SPECTRA is open to contributions that add parsers for additional scanner formats. See [Contributing](/docs/spectra/contributing/) for the contribution process and the project repository for the `parsers/` module structure.

---

### Scanner Comparison

| Feature | Trivy | Semgrep | Generic |
|---|---|---|---|
| Auto-detection | ✓ | ✓ | — |
| JSON structured input | ✓ | ✓ | — |
| CVE mapping | ✓ | ✓ (CWE) | Depends on input |
| SAST findings | — | ✓ | ✓ |
| Infrastructure/container | ✓ | — | ✓ |
| Pentest notes | — | — | ✓ |

---

**Next:** [Output Formats →](/docs/spectra/output-formats/)
