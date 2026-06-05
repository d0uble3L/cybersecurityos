---
title: "Contributing"
date: 2026-05-18
description: "How to contribute to SPECTRA — bug reports, feature requests, code contributions, and the contributor agreement."
draft: false
weight: 90
---

## Contributing to SPECTRA

SPECTRA is an open-source project and contributions are welcome. This page covers how to report issues, submit pull requests, and what to expect from the review process.

---

### Ways to Contribute

- **Bug reports** — Found something broken? Open an issue on GitHub
- **Feature requests** — Have an idea? Open a discussion or issue with context on your use case
- **New scanner parsers** — SPECTRA currently supports Trivy, Semgrep, and generic. Adding a new parser is one of the highest-value contributions
- **Documentation improvements** — Clarifications, examples, and corrections are always welcome
- **Test coverage** — Additional test cases for edge cases in scanner parsing or output rendering
- **Security vulnerability reports** — See the [Security Policy](#security-policy) below

---

### Getting Started

**1. Fork and clone the repository:**

```bash
git clone https://github.com/d0uble3L/spectra
cd spectra
```

**2. Create a development environment:**

```bash
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

**3. Run the test suite:**

```bash
pytest tests/
```

**4. Create a branch for your change:**

```bash
git checkout -b feature/my-contribution
```

---

### Contribution Guidelines

**Code style:** SPECTRA uses `ruff` for linting and formatting. Run `ruff check .` and `ruff format .` before submitting.

**Tests:** New parsers and features require test coverage. Use `tests/samples/` for sample scanner output files. Do not include real scan output from live systems.

**Commit messages:** Use the imperative mood, present tense. Example: `Add Grype scanner parser` not `Added Grype scanner parser`.

**Pull request scope:** Keep PRs focused on a single change. Large PRs are harder to review and slower to merge.

---

### Adding a New Scanner Parser

The parser module lives in `spectra/parsers/`. Each parser:

1. Receives raw scanner output (string or dict)
2. Returns a list of normalized `Finding` objects using the internal schema
3. Is registered in `spectra/parsers/__init__.py`

A new parser should include:

- Auto-detection logic (how SPECTRA recognizes this format without `--scanner`)
- Normalization to the `Finding` schema
- Sample output in `tests/samples/<scanner>/`
- Unit tests in `tests/test_parsers.py`

---

### Reporting Bugs

Open an issue at [github.com/d0uble3L/spectra/issues](https://github.com/d0uble3L/spectra/issues) and include:

- SPECTRA version (`spectra --version`)
- Operating system and Python version
- The command you ran (with any sensitive data redacted)
- The error output or unexpected behavior
- A minimal reproduction case if possible

---

### Security Policy

If you discover a security vulnerability in SPECTRA itself, **do not open a public GitHub issue.** Report it privately:

- Email: [hello@cybersecurityos.net](mailto:hello@cybersecurityos.net)
- Subject line: `[SPECTRA] Security Vulnerability Report`

Include a description of the vulnerability, reproduction steps, and your assessment of severity. You will receive an acknowledgment within 48 hours.

We follow responsible disclosure practices: we will coordinate a patch and public disclosure timeline with you before making any vulnerability public.

---

### Code of Conduct

Contributors are expected to engage respectfully and professionally. Harassment, discrimination, or abuse of any kind will not be tolerated. Issues and pull requests that violate these expectations will be closed and the contributor may be blocked from the project.

---

**Next:** [License →](/docs/spectra/license/)
