---
title: "Lab 8: Password Strength"
date: 2026-11-15
description: "Hands-on lab: estimate password strength locally after discounting common words, leet substitutions, keyboard and number sequences, and repeated patterns."
draft: false
weight: 80
group: "LABS"
lab: 8
navTitle: "Password Strength"
focus: "Credential hygiene"
summary: "Estimate strength after discounting common words, keyboard runs, and repeated patterns."
run: "python3 -m labs.password_strength.estimate"
source: "labs/password_strength"
---

## Lab 8: Password Strength

**Why it matters:** Length helps only when the characters are hard to guess. Attackers try common words, keyboard runs, number sequences, and repeated patterns first, often with substitutions such as `P@ssw0rd`. An 18-character password can still be weak.

### What you'll practice

- Why composition rules (upper, lower, digit, symbol) don't make passwords strong
- How guessing attacks exploit predictable patterns
- Why passphrases and password managers work

### Run it

From the root of the [CyberSHIELD Labs repository](https://github.com/d0uble3L/CyberSHIELD-Labs):

```bash
python3 -m labs.password_strength.estimate
```

Without arguments, the script asks for a password at a hidden prompt and never stores, logs, or sends it. With `--samples`, it rates the synthetic examples in `samples.txt`.

Example output:

```text
$ python3 -m labs.password_strength.estimate --samples
'Password123456789!': Weak (length 18, unpredictable length 1)
  - contains a common password word
  - contains a keyboard or alphabet/number sequence
'abcabcabcabcabcabc': Weak (length 18, unpredictable length 0)
  - contains a keyboard or alphabet/number sequence
  - repeats a short pattern
'Tr0ub4dor&3': Fair (length 11, unpredictable length 11)
'correct-horse-battery-staple': Strong (length 28, unpredictable length 28)
```

### What the checks look for

| Check               | Triggers when                                                                                                               |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Common words        | A small list of common password words, also matched after simple leet substitutions (`0`→`o`, `@`→`a`, `$`→`s`, and so on). |
| Sequences           | Runs of three or more adjacent keyboard, alphabet, or number characters, forwards or backwards (`qwerty`, `cba`, `1234`).   |
| Repeated characters | Three or more of the same character in a row (`aaa`).                                                                       |
| Repeated pattern    | The whole value repeating one short unit (`abcabcabc`).                                                                     |
| Rating              | Unpredictable length of 16+ is Strong, 12+ Good, 8+ Fair, otherwise Weak.                                                   |

### Try this

1. Run `--samples` and compare `Password123456789!` with `correct-horse-battery-staple`.
2. Add your own synthetic examples to a copy of `samples.txt` and pass its path: `--samples my-samples.txt`. Never put real passwords in a file.
3. Find a value the estimator rates Strong that you know is weak, such as a famous quote. What would a real checker need to catch it?

### What this lab doesn't do

This is a rough teaching heuristic, not an entropy calculation or a breach check. The word list is deliberately tiny, and a value rated Strong can still be weak, for example a well-known phrase or a password reused across sites.

### Takeaway

Prefer long, unique passwords or passphrases generated and stored by a password manager, and enable MFA where available.

> **Source:** [labs/password_strength](https://github.com/d0uble3L/CyberSHIELD-Labs/tree/master/labs/password_strength) on GitHub.
