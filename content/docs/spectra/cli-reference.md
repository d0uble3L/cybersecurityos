---
title: "CLI Reference"
date: 2026-05-18
description: "Complete command and flag reference for the SPECTRA CLI."
draft: false
weight: 30
---

## CLI Reference

Complete reference for all SPECTRA commands and flags.

---

### Global Usage

```
spectra [COMMAND] [OPTIONS] [INPUT]
```

---

### Commands

#### `spectra analyze`

Analyze a scanner output file and produce ranked findings, attack chain analysis, and an executive summary.

```
spectra analyze [INPUT] [OPTIONS]
```

**Arguments**

| Argument | Description                                               |
| -------- | --------------------------------------------------------- |
| `INPUT`  | Path to the scanner output file. Omit to read from stdin. |

**Options**

| Flag              | Type   | Default             | Description                                                                                                        |
| ----------------- | ------ | ------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `--scanner`, `-s` | string | `auto`              | Force scanner type: `trivy`, `semgrep`, `generic`. Auto-detected from file structure when omitted.                 |
| `--format`, `-f`  | string | `markdown`          | Output format: `markdown`, `json`, or `both`.                                                                      |
| `--output`, `-o`  | path   | _(stdout)_          | Output file path, without extension. SPECTRA appends `.md` and/or `.json` depending on `--format`.                 |
| `--model`, `-m`   | string | `claude-sonnet-5-5` | Claude model to use. Also read from `SPECTRA_MODEL`. See [Configuration](/docs/spectra/configuration/) for models. |
| `--think`, `-t`   | flag   | off                 | Request a summary of Claude's reasoning during analysis.                                                           |
| `--usage`, `-u`   | flag   | off                 | Print Anthropic API token usage (including cache reads and writes) after analysis. Useful for cost tracking.       |

**Examples**

```bash
# Basic Trivy analysis, Markdown output
spectra analyze trivy.json

# Both output formats, custom output path
spectra analyze trivy.json --format both --output reports/infra-scan-2026-05

# Force generic scanner for pentest notes
spectra analyze pentest.txt --scanner generic --format markdown --output reports/pt

# Pipe Semgrep JSON from stdin
cat semgrep.json | spectra analyze --scanner semgrep --format json --output reports/pr-42

# Print token usage after analysis
spectra analyze trivy.json --format both --output reports/run1 --usage
```

---

#### `spectra --version`

Print the installed SPECTRA version.

```bash
spectra --version
```

---

#### `spectra --help`

Print usage information.

```bash
spectra --help
spectra analyze --help
```

---

### Exit Codes

| Code | Meaning                                                                                          |
| ---- | ------------------------------------------------------------------------------------------------ |
| `0`  | Analysis completed successfully                                                                  |
| `1`  | Error: invalid or empty input, parse failure, or API error (authentication, rate limit, refusal) |
| `2`  | Invalid command-line usage (unknown flag or bad value)                                           |

---

### Environment Variable Overrides

These environment variables (or `.env` entries) are read. A CLI flag takes precedence over its variable:

| Environment Variable | Equivalent Flag |
| -------------------- | --------------- |
| `SPECTRA_MODEL`      | `--model`       |
| `ANTHROPIC_API_KEY`  | _(required)_    |

---

**Next:** [Configuration →](/docs/spectra/configuration/)
