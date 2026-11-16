---
title: "Contributing"
date: 2026-11-15
description: "How to contribute to CyberSHIELD Labs: report issues, improve checks, and propose new labs that follow the local-first, standard-library, synthetic-data conventions."
draft: false
weight: 210
group: "REFERENCE"
---

## Contributing

Contributions are welcome, from typo fixes to new labs.

### Report an issue

Open an issue on [GitHub](https://github.com/d0uble3L/CyberSHIELD-Labs/issues) with the lab name, the command you ran, what you expected, and what happened. For a check that misses something or fires wrongly, include a **synthetic** input that reproduces it.

### Improve a lab

1. Fork the repository and create a branch.
2. Make your change, and add or update a test in `tests/test_labs.py`.
3. Run `python3 -m unittest discover -s tests -v` and confirm everything passes.
4. Update the lab's `README.md` if behaviour changes.
5. Open a pull request that explains what the change teaches or fixes.

### Propose a new lab

New labs follow the same conventions as the existing ones:

- **Package layout:** `labs/<lab_name>/` with `__init__.py`, a guide (`README.md`), one runnable script, and a synthetic fixture.
- **Runnable as a module:** `python3 -m labs.<lab_name>.<script>`, reading the bundled fixture by default and accepting a path to the learner's own copy.
- **Standard library only:** no third-party dependencies.
- **Local and synthetic:** no external network calls, no real data, no payloads.
- **Guide structure:** _Why it matters_, _Exercise_, honest limitations, and a one-line _Takeaway_.
- **Tests:** at least one test that shows the check firing, and one that shows it staying quiet on a safe input.

Open an issue describing the skill the lab teaches before you start, so we can agree on scope.
