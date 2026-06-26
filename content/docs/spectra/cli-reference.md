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

| Flag           | Type   | Default             | Description                                                                                        |
| -------------- | ------ | ------------------- | -------------------------------------------------------------------------------------------------- |
| `--scanner`    | string | auto                | Force scanner type: `trivy`, `semgrep`, `generic`. Auto-detected from file structure when omitted. |
| `--format`     | string | `markdown`          | Output format: `markdown`, `json`, or `both`.                                                      |
| `--output`     | string | `./spectra_report`  | Output file path, without extension. SPECTRA appends `.md` and/or `.json` depending on `--format`. |
| `--usage`      | flag   | off                 | Print Anthropic API token usage stats after analysis. Useful for cost tracking.                    |
| `--model`      | string | `claude-sonnet-4-6` | Claude model to use. See [Configuration](/docs/spectra/configuration/) for supported models.       |
| `--max-tokens` | int    | 4096                | Maximum tokens for the AI response. Increase for very large scan files.                            |
| `--verbose`    | flag   | off                 | Enable verbose logging, including prompt and response details.                                     |

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

| Code | Meaning                                                  |
| ---- | -------------------------------------------------------- |
| `0`  | Analysis completed successfully                          |
| `1`  | General error (invalid input, missing API key, etc.)     |
| `2`  | API error (Anthropic rate limit, authentication failure) |

---

### Environment Variable Overrides

All `--flag` options can also be set via environment variables, which take lower precedence than CLI flags:

| Environment Variable | Equivalent Flag |
| -------------------- | --------------- |
| `SPECTRA_SCANNER`    | `--scanner`     |
| `SPECTRA_FORMAT`     | `--format`      |
| `SPECTRA_OUTPUT`     | `--output`      |
| `SPECTRA_MODEL`      | `--model`       |
| `ANTHROPIC_API_KEY`  | _(required)_    |

---

**Next:** [Configuration →](/docs/spectra/configuration/)
