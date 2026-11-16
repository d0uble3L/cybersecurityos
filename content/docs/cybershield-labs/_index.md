---
title: "CyberSHIELD Labs"
date: 2026-11-15
description: "CyberSHIELD Labs: nine free, open-source, hands-on defensive security labs covering AI application safety, cloud IAM, identity threat hunting, DLP, backup resilience, SBOMs, containers, passwords, and web exposure. Standard-library Python, synthetic data, runs locally."
layout: "labs-landing"
draft: false
cascade:
  layout: "labs-doc"
---

## CyberSHIELD Labs Documentation

**CyberSHIELD Labs** are small, local-first exercises for practical defensive security. Each lab has a short guide, a runnable standard-library Python script, and a synthetic fixture.

> **License:** Apache License 2.0
> **Source:** [github.com/d0uble3L/CyberSHIELD-Labs](https://github.com/d0uble3L/CyberSHIELD-Labs) > **Requirements:** Python 3.9 or later, no other dependencies

### Labs

| #   | Lab                                                                            | Focus              | Run                                              |
| --- | ------------------------------------------------------------------------------ | ------------------ | ------------------------------------------------ |
| 1   | [AI Application Safety](/docs/cybershield-labs/ai-application-safety/)         | AI agents          | `python3 -m labs.ai_application_safety.analyze`  |
| 2   | [Cloud IAM Posture](/docs/cybershield-labs/cloud-iam-posture/)                 | Least privilege    | `python3 -m labs.cloud_iam_posture.audit`        |
| 3   | [Identity Threat Hunting](/docs/cybershield-labs/identity-threat-hunting/)     | Identity & MFA     | `python3 -m labs.identity_threat_hunting.hunt`   |
| 4   | [Sensitive-Data Protection](/docs/cybershield-labs/sensitive-data-protection/) | DLP & privacy      | `python3 -m labs.sensitive_data_protection.scan` |
| 5   | [Backup Resilience](/docs/cybershield-labs/backup-resilience/)                 | Recovery           | `python3 -m labs.backup_resilience.validate`     |
| 6   | [Software Supply Chain](/docs/cybershield-labs/software-supply-chain/)         | SBOM quality       | `python3 -m labs.software_supply_chain.audit`    |
| 7   | [Container Configuration](/docs/cybershield-labs/container-configuration/)     | Workload hardening | `python3 -m labs.container_configuration.audit`  |
| 8   | [Password Strength](/docs/cybershield-labs/password-strength/)                 | Credential hygiene | `python3 -m labs.password_strength.estimate`     |
| 9   | [Web Exposure](/docs/cybershield-labs/web-exposure/)                           | Attack surface     | `python3 -m labs.web_exposure.check --demo`      |

### Reference

- [Getting Started](/docs/cybershield-labs/getting-started/)
- [Safety and Scope](/docs/cybershield-labs/safety-and-scope/)
- [Contributing](/docs/cybershield-labs/contributing/)
- [License](/docs/cybershield-labs/license/)
