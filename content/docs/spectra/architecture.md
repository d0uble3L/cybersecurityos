---
title: "Architecture"
date: 2026-05-18
description: "SPECTRA design decisions — prompt caching, AI layer, parser architecture, and data flow."
draft: false
weight: 80
---

## Architecture

This page covers SPECTRA's internal design: how data flows through the system, how the AI layer works, and the key engineering decisions behind the implementation.

---

### High-Level Data Flow

```
Scanner Output (Trivy JSON / Semgrep JSON / plain text)
        ↓
    Parser Layer
  (format detection + normalization)
        ↓
  Context Builder
  (finding enrichment + chain detection input)
        ↓
  Claude API (with prompt caching)
  (triage, attack chain analysis, executive summary)
        ↓
  Report Renderer
  (Markdown + JSON output)
        ↓
  Output Files / stdout
```

---

### Parser Layer

SPECTRA includes a parser for each supported scanner format. Parsers normalize raw scanner output into a common internal schema before it reaches the AI layer:

```python
# Internal finding schema (simplified)
{
  "id": str,            # CVE ID, rule ID, or generated ID
  "scanner": str,       # Source scanner type
  "severity": str,      # normalized: critical/high/medium/low/informational
  "title": str,
  "description": str,
  "affected": str,      # Package, file path, or host
  "installed": str,     # Installed version (if applicable)
  "fixed": str,         # Fixed version (if applicable)
  "cvss_score": float,  # Raw CVSS if available
  "raw": dict           # Original finding object from scanner
}
```

This normalization layer is why SPECTRA can provide consistent analysis quality regardless of which scanner produced the input.

---

### AI Layer — Claude Integration

The normalized findings are passed to Claude using a structured system prompt that encodes the security analyst role, prioritization criteria, and output format requirements.

**Analyst system prompt responsibilities:**
- Define the triage methodology (EPSS-weighted, attack-chain-aware, context-calibrated)
- Specify output structure (executive summary, ranked findings, attack chains, remediation)
- Set the audience model (engineer-level vs. CISO-level language calibration)

**User message:**  
The normalized findings JSON for the current scan run.

**Model response:**  
Structured analysis that is parsed back into the report schema.

---

### Prompt Caching

SPECTRA uses [Claude's prompt caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) to make repeated analysis economically viable.

The analyst system prompt — which is large and static — is cached after the first API call. Subsequent calls in the same session reuse the cached prefix, reducing:

- **Latency** by ~30–50% on cache hits
- **Cost** by approximately 90% for the cached portion of each request

This is the mechanism that makes SPECTRA viable for continuous pipeline use at production scan volumes. Without caching, running analysis on every PR would be cost-prohibitive for most teams.

**Cache behavior:**
- Cache is populated on the first call with a given system prompt
- The cache TTL is 5 minutes for standard caching; extended caching (up to 1 hour) is available with `cache_control: {"type": "ephemeral"}` on the system block
- Token usage output (`--usage`) shows `cache_read_input_tokens` and `cache_creation_input_tokens` so you can monitor cache efficiency

---

### Attack Chain Detection

Attack chain detection is performed by Claude during analysis, not by a separate rule engine. The prompt instructs Claude to:

1. Identify findings that share a common host, service, or package scope
2. Evaluate whether the findings could be sequentially exploited (e.g., unauthenticated RCE → privilege escalation → lateral movement)
3. Assign a combined severity to the chain that may be higher than any individual finding's severity

This approach is intentionally model-driven rather than rule-driven. Rule-based chain detection requires maintaining a library of attack patterns; model-driven detection generalizes across patterns the rules don't cover, including novel chains.

---

### Output Rendering

After the AI layer returns a response, SPECTRA's renderer:

1. Parses the structured response into the internal report schema
2. Renders the Markdown report using a template engine
3. Serializes the JSON report directly from the schema
4. Writes outputs to the specified path

The rendering layer is decoupled from the AI layer, making it straightforward to add new output formats (HTML, PDF, SARIF) without modifying the analysis pipeline.

---

### Design Principles

**Upstream neutrality** — SPECTRA does not prescribe which scanners you use. Add or remove scanner integrations without changing the AI or rendering layers.

**AI as analyst, not gatekeeper** — SPECTRA never blocks pipelines on its own judgment. It surfaces findings and analysis; humans and pipeline configuration decide what to do with them.

**Minimal footprint** — SPECTRA has no persistent state, no database, and no required network services beyond the Anthropic API. It reads input, calls the API, and writes output. Nothing else.

**Cost transparency** — The `--usage` flag exposes full token accounting so operators can track AI costs per scan and optimize accordingly.

---

**Next:** [Contributing →](/docs/spectra/contributing/)
