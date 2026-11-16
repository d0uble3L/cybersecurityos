---
title: "Lab 7: Container Configuration"
date: 2026-11-15
description: "Hands-on lab: statically review a Dockerfile for floating base images, root users, secrets in ENV, and ADD instructions."
draft: false
weight: 70
group: "LABS"
lab: 7
navTitle: "Container Configuration"
focus: "Workload hardening"
summary: "Statically review a Dockerfile for floating base images, root users, and embedded secrets."
run: "python3 -m labs.container_configuration.audit"
source: "labs/container_configuration"
---

## Lab 7: Container Configuration

**Why it matters:** Container build settings decide a workload's privileges, how repeatable its build is, and what it exposes. A floating base tag, a root user, or a baked-in secret can undo a lot of runtime hardening.

### What you'll practice

- Reviewing Dockerfiles like a platform security engineer
- Pinning base images by tag or digest
- Running workloads as non-root

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.container_configuration.audit
```

The bundled `Dockerfile` uses `FROM python:latest`, sets a fake `DEMO_API_KEY` in `ENV`, copies a file with `ADD`, and ends with `USER root`.

Example output:

```text
$ python3 -m labs.container_configuration.audit
[REVIEW] line 1: floating_base_image
[REVIEW] line 2: possible_secret_in_env
[REVIEW] line 3: review_add_instruction
[REVIEW] line 4: root_user
```

### What the checks look for

| Check                        | Triggers when                                                                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `floating_base_image`        | `FROM` uses `:latest` or has no tag or digest at all (which implies `latest`). Build args, `scratch`, and earlier build stages are skipped. |
| `root_user`                  | `USER root`, `USER 0`, or the `root:root` / `0:0` user:group forms.                                                                         |
| `possible_secret_in_env`     | `ENV` keys that look like `PASSWORD`, `TOKEN`, `SECRET`, or `API_KEY`.                                                                      |
| `review_add_instruction`     | An `ADD` instruction, which can fetch remote URLs and unpack archives; `COPY` is usually safer.                                             |
| `non_root_user_not_declared` | No `USER` instruction anywhere.                                                                                                             |

### Try this

1. Pin the base image to a specific version (or a digest), replace `ADD` with `COPY`, remove the secret, and add a non-root user in a copy of the file.
2. Try `FROM python` with no tag. It's flagged, because no tag means `latest`.
3. Write a multi-stage build (`FROM python:3.12 AS build` then `FROM build`) and confirm the stage reference isn't flagged.

### What this lab doesn't do

The checks are intentionally simple. They don't fully parse Dockerfile syntax, build images, inspect registries, or assess runtime settings such as capabilities, seccomp, or read-only filesystems. The fake key is not a credential.

### Takeaway

Pin and verify build inputs, avoid embedding secrets, run with least privilege, and use a maintained image-scanning pipeline.

> **Source:** [labs/container_configuration](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/container_configuration) on GitHub.
