---
title: "Installation"
date: 2026-05-18
description: "How to install SPECTRA — system requirements, pip install, Docker, and source build."
draft: false
weight: 10
---

## Installation

SPECTRA runs anywhere Python 3.9+ is available. Three install paths are supported.

---

### System Requirements

| Requirement       | Minimum                                                   |
| ----------------- | --------------------------------------------------------- |
| Python            | 3.9+                                                      |
| pip               | 21.0+                                                     |
| Anthropic API key | Required ([get one here](https://console.anthropic.com/)) |
| Operating system  | Linux, macOS, Windows (WSL2 recommended)                  |
| Memory            | 512 MB RAM minimum                                        |
| Disk              | 100 MB for source + dependencies                          |

---

### Option 1 — pip (Recommended)

```bash
git clone https://github.com/d0uble3L/spectra
cd spectra
pip install -e .
```

The `-e` flag installs in editable mode, making it easy to pull updates with `git pull` without reinstalling.

Verify the install:

```bash
spectra --version
```

---

### Option 2 — Docker

No local Python environment required. Mount your scan files and receive reports in the `reports/` directory.

```bash
# Pull and run a one-off analysis
docker compose run --rm analyze scans/trivy.json --format both --output /app/reports/out
```

Build the image locally from source:

```bash
git clone https://github.com/d0uble3L/spectra
cd spectra
docker build -t spectra .
docker run --rm \
  -v $(pwd)/scans:/app/scans \
  -v $(pwd)/reports:/app/reports \
  --env-file .env \
  spectra analyze scans/trivy.json --format both --output /app/reports/out
```

---

### Option 3 — Source (development)

```bash
git clone https://github.com/d0uble3L/spectra
cd spectra
python -m venv .venv
source .venv/bin/activate    # Windows: .venv\Scripts\activate
pip install -e ".[dev]"
```

The `[dev]` extras install test dependencies (`pytest`, `ruff`, `mypy`).

---

### Environment Setup

SPECTRA reads credentials from a `.env` file in the project root.

```bash
cp .env.example .env
```

Open `.env` and add your Anthropic API key:

```bash
ANTHROPIC_API_KEY=sk-ant-...
```

Keep this file out of version control — it is already listed in `.gitignore`.

> For CI/CD environments, inject `ANTHROPIC_API_KEY` as a repository secret rather than committing a `.env` file. See [CI/CD Integration](/docs/spectra/cicd-integration/) for details.

---

### Verifying the Installation

Run SPECTRA against the bundled sample file to confirm everything works:

```bash
spectra analyze tests/samples/trivy_sample.json
```

A successful run prints a ranked finding summary to stdout and writes reports to the current directory.

---

**Next:** [Quick Start →](/docs/spectra/quickstart/)
