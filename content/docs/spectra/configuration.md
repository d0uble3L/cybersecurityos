---
title: "Configuration"
date: 2026-05-18
description: "Environment variables, .env setup, and model configuration for SPECTRA."
draft: false
weight: 40
---

## Configuration

SPECTRA is configured through a `.env` file in the project root and optionally through environment variables set in the shell or CI/CD pipeline.

---

### `.env` File

Copy the template and populate your values:

```bash
cp .env.example .env
```

A complete `.env` file looks like this:

```bash
# Required
ANTHROPIC_API_KEY=sk-ant-...

# Optional — override defaults
SPECTRA_MODEL=claude-sonnet-4-6
SPECTRA_FORMAT=both
SPECTRA_OUTPUT=reports/latest
SPECTRA_MAX_TOKENS=4096
```

> Never commit `.env` to version control. It is listed in `.gitignore` by default.

---

### Required Variables

| Variable            | Description                                                                                    |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| `ANTHROPIC_API_KEY` | Your Anthropic API key. Obtain one at [console.anthropic.com](https://console.anthropic.com/). |

---

### Optional Variables

| Variable             | Default             | Description                                                   |
| -------------------- | ------------------- | ------------------------------------------------------------- |
| `SPECTRA_MODEL`      | `claude-sonnet-4-6` | Claude model to use for analysis. See supported models below. |
| `SPECTRA_FORMAT`     | `markdown`          | Default output format: `markdown`, `json`, or `both`.         |
| `SPECTRA_OUTPUT`     | `./spectra_report`  | Default output path prefix.                                   |
| `SPECTRA_MAX_TOKENS` | `4096`              | Maximum response tokens. Increase for large scans.            |
| `SPECTRA_SCANNER`    | _(auto)_            | Force scanner detection: `trivy`, `semgrep`, `generic`.       |

---

### Supported Claude Models

| Model                       | ID                          | Notes                                                              |
| --------------------------- | --------------------------- | ------------------------------------------------------------------ |
| Claude Sonnet 4.6 (default) | `claude-sonnet-4-6`         | Best balance of speed and quality for most analysis workloads      |
| Claude Opus 4.7             | `claude-opus-4-7`           | Highest quality — recommended for complex multi-scanner batch runs |
| Claude Haiku 4.5            | `claude-haiku-4-5-20251001` | Fastest and lowest cost — suitable for high-volume pipeline use    |

Override the model per-run with the `--model` flag:

```bash
spectra analyze trivy.json --model claude-opus-4-7 --format both --output reports/deep-analysis
```

---

### CI/CD Configuration

In CI/CD environments, inject `ANTHROPIC_API_KEY` as a repository secret — do not store it in `.env` files committed to your repository.

See [CI/CD Integration](/docs/spectra/cicd-integration/) for platform-specific examples.

---

### Configuration Precedence

When the same option is set in multiple places, SPECTRA resolves in this order (highest to lowest):

1. CLI flag (`--format`, `--output`, etc.)
2. Shell environment variable
3. `.env` file value
4. Built-in default

---

**Next:** [Supported Scanners →](/docs/spectra/scanners/)
