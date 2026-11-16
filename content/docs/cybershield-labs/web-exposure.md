---
title: "Lab 9: Web Exposure"
date: 2026-11-15
description: "Hands-on lab: check a local practice server for commonly exposed paths such as .git/, backups, and admin pages, with a built-in demo server and a loopback-only safeguard."
draft: false
weight: 90
group: "LABS"
lab: 9
navTitle: "Web Exposure"
focus: "Attack surface"
summary: "Check a local practice server for exposed .git/, backups, and admin paths. Loopback only."
run: "python3 -m labs.web_exposure.check --demo"
source: "labs/web_exposure"
---

## Lab 9: Web Exposure

**Why it matters:** Forgotten admin pages, backup folders, `.git/` directories, and debug configs are among the most common ways web servers leak information. An exposed `.git/` can hand over your entire source history. Checking your own servers for them is basic hygiene.

### What you'll practice

- Recognizing commonly exposed web paths
- Interpreting HTTP status codes with care
- Building safety guardrails into security tooling

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.web_exposure.check --demo
```

`--demo` starts a temporary practice server on `127.0.0.1` (on a random free port) with synthetic files, sends one `HEAD` request for each of 18 common paths, prints the results, and shuts the server down. To check a server you run yourself, pass its URL instead: `python3 -m labs.web_exposure.check http://127.0.0.1:8000`.

Example output:

```text
$ python3 -m labs.web_exposure.check --demo
Checking http://127.0.0.1:55823 ...

[+] Access controlled (might exist) (403): http://127.0.0.1:55823/admin/
[+] Found (200): http://127.0.0.1:55823/.git/
[+] Found (200): http://127.0.0.1:55823/robots.txt
[+] Found (200): http://127.0.0.1:55823/config.json
[+] Found (200): http://127.0.0.1:55823/backup/
```

### What the checks look for

| Check                 | Triggers when                                                                                        |
| --------------------- | ---------------------------------------------------------------------------------------------------- |
| Found                 | The path returned `200`.                                                                             |
| Access controlled     | The path returned `401` or `403`. That's only a response code; it doesn't prove the resource exists. |
| Redirect not followed | The path returned a `3xx` redirect, which the checker reports but never follows.                     |

### Try this

1. Run `--demo`, then start `python3 -m http.server 8000 --bind 127.0.0.1` in an empty folder and check that. Compare the results.
2. Try `https://example.com`. The checker refuses it. Read `normalize_base_url` to see how the loopback-only guard works.
3. Some servers return `200` for every path. How would you tell a real hit from a catch-all page?

### What this lab doesn't do

The checker accepts only `localhost` or loopback IP addresses and refuses credentials, query strings, and fragments in the URL. It ignores `HTTP(S)_PROXY` settings so requests never leave your machine, doesn't follow redirects, and uses a short timeout. It is not a vulnerability scanner or a substitute for an authorized assessment. Don't modify the local-only safeguard.

### Takeaway

Keep deployable artifacts out of web roots, block version-control and backup paths at the server, and assess only systems you own or are explicitly authorized to test.

> **Source:** [labs/web_exposure](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/web_exposure) on GitHub.
