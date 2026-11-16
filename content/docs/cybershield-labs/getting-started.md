---
title: "Getting Started"
date: 2026-11-15
description: "Set up CyberSHIELD Labs in under a minute: clone the repository, run your first lab with standard-library Python, and run the test suite."
draft: false
weight: 1
group: "START"
---

## Getting Started

CyberSHIELD Labs need only **Python 3.9 or later**. There's nothing to install: no packages, containers, accounts, or API keys.

### 1. Check your Python version

```bash
python3 --version
```

### 2. Clone the repository

```bash
git clone https://github.com/d0uble3L/CyberSHIELD-Labs
cd CyberSHIELD-Labs
```

### 3. Run your first lab

Every lab runs from the repository root as a Python module. Lab 9 is a good first run because it starts its own throwaway practice server:

```bash
python3 -m labs.web_exposure.check --demo
```

Or start at the beginning of the learning path:

```bash
python3 -m labs.ai_application_safety.analyze
```

### 4. Bring your own input

Most labs read a bundled synthetic fixture by default and accept a path to your own copy. Copy the fixture, edit it, and pass it as an argument:

```bash
cp labs/cloud_iam_posture/policy.json /tmp/my-policy.json
python3 -m labs.cloud_iam_posture.audit /tmp/my-policy.json
```

Use only synthetic data. Never put real credentials, personal data, or production configs into a lab.

### 5. Run the tests

```bash
python3 -m unittest discover -s tests -v
```

### Repository layout

```text
labs/<lab_name>/README.md   guide for the exercise
labs/<lab_name>/*.py        runnable script (python3 -m labs.<lab_name>.<script>)
labs/<lab_name>/*.json|txt  synthetic fixture
tests/test_labs.py          unit tests for every lab
```

### Suggested path

Work through the labs in order. Each takes about 15–30 minutes, including the "Try this" exercises. If you're short on time, start with the area closest to your role:

| If you work on…            | Start with                                                                            |
| -------------------------- | ------------------------------------------------------------------------------------- |
| AI features or agents      | [Lab 1: AI Application Safety](/docs/cybershield-labs/ai-application-safety/)         |
| Cloud infrastructure       | [Lab 2: Cloud IAM Posture](/docs/cybershield-labs/cloud-iam-posture/)                 |
| SOC or detection           | [Lab 3: Identity Threat Hunting](/docs/cybershield-labs/identity-threat-hunting/)     |
| Privacy or data governance | [Lab 4: Sensitive-Data Protection](/docs/cybershield-labs/sensitive-data-protection/) |
| Resilience or GRC          | [Lab 5: Backup Resilience](/docs/cybershield-labs/backup-resilience/)                 |
| AppSec or DevSecOps        | [Lab 6: Software Supply Chain](/docs/cybershield-labs/software-supply-chain/)         |
| Platform or containers     | [Lab 7: Container Configuration](/docs/cybershield-labs/container-configuration/)     |
| Security awareness         | [Lab 8: Password Strength](/docs/cybershield-labs/password-strength/)                 |
| Web applications           | [Lab 9: Web Exposure](/docs/cybershield-labs/web-exposure/)                           |
