---
title: "CI/CD Integration"
date: 2026-05-18
description: "Integrate SPECTRA into GitHub Actions, GitLab CI, and Jenkins pipelines."
draft: false
weight: 70
---

## CI/CD Integration

SPECTRA's CLI-first design makes it a natural fit in automated pipelines. This page covers integration patterns for GitHub Actions, GitLab CI, and Jenkins.

---

### General Principles

1. **Inject `ANTHROPIC_API_KEY` as a repository secret** — never hard-code credentials in pipeline YAML
2. **Use `--format json`** for machine-readable output and pipe to ticket creation or SIEM ingest
3. **Use `--format both`** to preserve a human-readable artifact for security team review
4. **Run SPECTRA after your scanner step**, not before — it consumes scanner output, not raw code

---

### GitHub Actions

#### Trivy + SPECTRA in a PR check

```yaml
# .github/workflows/security-scan.yml
name: Security Scan

on:
  pull_request:
    branches: [main, develop]

jobs:
  spectra-analysis:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Trivy container scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ github.repository }}:${{ github.sha }}
          format: json
          output: trivy.json
          exit-code: 0

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install SPECTRA
        run: |
          git clone https://github.com/d0uble3L/spectra /tmp/spectra
          pip install -e /tmp/spectra

      - name: Run SPECTRA analysis
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          spectra analyze trivy.json --format both --output reports/pr-${{ github.event.pull_request.number }}

      - name: Upload SPECTRA report
        uses: actions/upload-artifact@v4
        with:
          name: spectra-report-pr-${{ github.event.pull_request.number }}
          path: reports/
          retention-days: 30
```

#### Semgrep + SPECTRA on push

```yaml
# .github/workflows/sast.yml
name: SAST

on:
  push:
    branches: [main]

jobs:
  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Semgrep
        run: |
          pip install semgrep
          semgrep --config=auto --json > semgrep.json

      - name: Install SPECTRA
        run: |
          git clone https://github.com/d0uble3L/spectra /tmp/spectra
          pip install -e /tmp/spectra

      - name: Run SPECTRA analysis
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          cat semgrep.json | spectra analyze --scanner semgrep --format both --output reports/sast-${{ github.run_id }}

      - name: Upload report
        uses: actions/upload-artifact@v4
        with:
          name: spectra-sast-${{ github.run_id }}
          path: reports/
```

---

### GitLab CI

```yaml
# .gitlab-ci.yml
spectra-analysis:
  stage: security
  image: python:3.11-slim
  before_script:
    - apt-get update && apt-get install -y git
    - git clone https://github.com/d0uble3L/spectra /tmp/spectra
    - pip install -e /tmp/spectra
  script:
    - trivy image $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA -f json -o trivy.json
    - spectra analyze trivy.json --format both --output reports/pipeline-$CI_PIPELINE_ID
  artifacts:
    paths:
      - reports/
    expire_in: 30 days
  variables:
    ANTHROPIC_API_KEY: $ANTHROPIC_API_KEY # Set in GitLab CI/CD variables
```

---

### Jenkins (Declarative Pipeline)

```groovy
// Jenkinsfile
pipeline {
    agent any
    environment {
        ANTHROPIC_API_KEY = credentials('anthropic-api-key')
    }
    stages {
        stage('Trivy Scan') {
            steps {
                sh 'trivy image your-image:latest -f json -o trivy.json'
            }
        }
        stage('SPECTRA Analysis') {
            steps {
                sh '''
                    pip install -e /opt/spectra
                    spectra analyze trivy.json --format both --output reports/build-${BUILD_NUMBER}
                '''
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
        }
    }
}
```

---

### Secrets Management

| Platform       | How to add `ANTHROPIC_API_KEY`                                     |
| -------------- | ------------------------------------------------------------------ |
| GitHub Actions | Settings → Secrets and variables → Actions → New repository secret |
| GitLab CI      | Settings → CI/CD → Variables → Add variable                        |
| Jenkins        | Manage Jenkins → Credentials → Add Credentials (Secret text)       |

---

### Recommended Pipeline Placement

```
Commit → Build → [ Trivy / Semgrep ] → SPECTRA → Report artifact → (optional) Ticket creation
```

SPECTRA runs after the scanner to analyze its output. It does not block the build by default — set your pipeline to fail on findings by parsing the JSON output for `critical` severity items if a gate is desired.

---

**Next:** [Architecture →](/docs/spectra/architecture/)
