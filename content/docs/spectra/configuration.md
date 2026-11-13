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

# Optional — override the default model
SPECTRA_MODEL=claude-sonnet-5-5
```

> Never commit `.env` to version control. It is listed in `.gitignore` by default.

---

### Required Variables

| Variable            | Description                                                                                    |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| `ANTHROPIC_API_KEY` | Your Anthropic API key. Obtain one at [console.anthropic.com](https://console.anthropic.com/). |

---

### Optional Variables

| Variable        | Default             | Description                                                                                        |
| --------------- | ------------------- | -------------------------------------------------------------------------------------------------- |
| `SPECTRA_MODEL` | `claude-sonnet-5-5` | Claude model to use for analysis, for both the CLI and the API server. See supported models below. |

Output format, output path, and scanner type are set per run with `--format`, `--output`, and `--scanner`. See the [CLI Reference](/docs/spectra/cli-reference/).

---

### Supported Claude Models

| Model                       | ID                  | Notes                                                                      |
| --------------------------- | ------------------- | -------------------------------------------------------------------------- |
| Claude Sonnet 5.5 (default) | `claude-sonnet-5-5` | Best balance of quality, speed, and cost; recommended for CI/CD pipelines  |
| Claude Opus 5.5             | `claude-opus-5-5`   | Highest quality; recommended for complex multi-scanner or large batch runs |
| Claude Haiku 5.5            | `claude-haiku-5-5`  | Lowest cost, for high-volume pipelines; no automatic refusal fallback      |

Override the model per run with the `--model` flag:

```bash
spectra analyze trivy.json --model claude-opus-5-5 --format both --output reports/deep-analysis
```

#### Safety refusals and fallback

Scanner output describes vulnerabilities and exploits, so a request can occasionally be declined by Claude's real-time cyber safeguards. SPECTRA turns on server-side fallbacks, so a declined request is automatically retried on a fallback model within the same API call. If the request is still declined, the run fails with a message naming the refusal category. Haiku has no server-side fallback. For legitimate security work that is declined repeatedly, see the [Cyber Verification Program](https://support.claude.com/en/articles/14604842-real-time-cyber-safeguards-on-claude-opus-and-sonnet).

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
