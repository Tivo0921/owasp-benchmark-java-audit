# Participant Guide

## Purpose of this experiment

We are comparing how well AI coding agents perform **static security code
audits**. You will read individual Java testcases from OWASP BenchmarkJava and,
for each one, write a structured vulnerability report.

This is **not** a capture-the-flag or exploitation exercise:

- You do **not** need to capture any flag.
- You do **not** need to write or run an exploit.
- You do **not** run the application or any scanner.

You only **read the Java source code** and reason about whether it is
vulnerable. Some cases are genuinely vulnerable; others are deliberate
*false positives* that look risky but are safe. Deciding which is which is the
core of the task.

## Repository contents — READ THE DEPENDENCIES

The testcases do **not** stand alone. They reference shared code and config by
fully-qualified name, and the verdict often depends on it:

- `cases/` — the `BenchmarkTestNNNNN.java` files you must audit.
- `helpers/` — source for the `org.owasp.benchmark.helpers` package the cases
  call into. **You must read these to resolve sources/sinks.** For example
  `SeparateClassRequest.getTheValue()` returns a constant **safe** value while
  `getTheParameter()` is a genuine **tainted** source; `DatabaseHelper` and
  `LDAPManager` are the real sinks.
- `benchmark.properties` — configuration the helpers load. It defines, e.g.,
  which hashing/crypto algorithm is actually used (`MD5` = weak vs `SHA-256` =
  strong). **Hash/crypto verdicts depend on this file.**

If you judge a case without consulting the referenced helper/property, you may
get the verdict wrong — that is by design.

## What you are evaluated on

For each case we score whether you can:

1. determine whether a real vulnerability exists (`is_vulnerable`),
2. name the vulnerability category (`bug_type`),
3. identify the CWE (`cwe`),
4. explain the root cause (`root_cause`), and
5. propose a fix (`patch_strategy`).

## How scoring works (read carefully)

Each case is worth **3 points**, scored automatically:

| Field                 | Points | Awarded when …                                |
| --------------------- | ------ | --------------------------------------------- |
| `is_vulnerable`       | 1      | matches the ground truth (`true`/`false`)     |
| `cwe`                 | 1      | the CWE id matches the case's category        |
| `bug_type` / category | 1      | maps to the case's vulnerability category     |

`root_cause` and `patch_strategy` are **not auto-scored** but are recorded for
qualitative review — fill them in.

### IMPORTANT — cases that are NOT vulnerable (false positives)

Every case **belongs to one vulnerability category** (e.g. SQL Injection),
**even when the code is actually safe**. The "safe" cases are deliberate false
positives: code that looks like that category's bug but has been made safe
(input validated, safe API used, etc.).

So for a case you judge **safe**:

- set `is_vulnerable: false`, **and**
- still fill `bug_type` and `cwe` with **the category the code is about**
  (the vulnerability class it superficially resembles / is testing).

In other words: `is_vulnerable` answers *"is this actually exploitable?"*, while
`bug_type`/`cwe` answer *"which vulnerability class is this code concerned
with?"*. **Do not leave `cwe` / `bug_type` blank on safe cases** — you would
lose those 2 points even though your safe/vulnerable verdict was correct.

Example — a safe SQL-Injection-style case:

```json
{ "is_vulnerable": false, "bug_type": "SQL Injection", "cwe": "CWE-89", "...": "..." }
```

## Which AI agents you may use

You may use any AI coding agent or LLM-based assistant (e.g. Claude Code, an
IDE agent, a chat model). You must **record exactly what you used**:

- `model_or_agent` — the tool/agent (e.g. `claude-code`, `cursor`, `chatgpt`).
- `model_name` — the specific model (e.g. `claude-opus-4-8`).
- `web_search_used` — whether the agent used web search (see below).
- Human intervention level — how much a human helped (see *Recording* below).

## Web search rule (default: NOT allowed)

The default condition is **closed-book**: the agent may use only the Java code
provided in `cases/`. **Web search / browsing is disabled.**

If the organizer explicitly runs an *open-book* round, you may enable web
search, but you must set `"web_search_used": true` in every report so the two
conditions can be compared. When in doubt, keep web search **off**.

### Why this matters

The OWASP Benchmark answer key is **publicly available on the internet**. The
validity of a closed-book run therefore depends entirely on the **agent not
looking anything up during the run** — not on the answers being secret. A
closed-book result is only meaningful if the agent:

1. **does not use web search / browsing**, and
2. **only reads files inside this repository** (the `cases/` folder) — it must
   not open other directories, sibling repos, or the organizer's grading repo.

### Enforcing closed-book (Claude Code)

This repo ships a `.claude/settings.json` that **denies `WebSearch` and
`WebFetch`** so a Claude Code session started here cannot browse. To keep the
run clean:

- Clone and open **only this repository**; run the agent with this folder as the
  working directory.
- Do **not** add other directories to the agent's context (no `--add-dir`, no
  pasting external files).
- Keep the shipped `.claude/settings.json` in place; do not re-enable web tools.

For other agents/IDEs, apply the equivalent: disable web/browse tools and scope
file access to this folder only. Then set `"web_search_used": false` honestly.

## Submission format

Produce **one JSON file per case**, named after the test, e.g.
`BenchmarkTest00027.json`. Each file follows
`report_template.json`:

```json
{
  "test_name": "BenchmarkTest00027",
  "model_or_agent": "claude-code",
  "model_name": "claude-opus-4-8",
  "web_search_used": false,
  "is_vulnerable": true,
  "bug_type": "SQL Injection",
  "cwe": "CWE-89",
  "vulnerable_file": "BenchmarkTest00027.java",
  "vulnerable_function": "doPost",
  "root_cause": "Request parameter is concatenated into a SQL statement without parameterization.",
  "security_impact": "An attacker can inject SQL to read or modify the database.",
  "patch_strategy": "Use a PreparedStatement with bound parameters.",
  "confidence": 0.9
}
```

Field notes:

- `is_vulnerable` — `true` or `false` (use your best judgement; `null` scores 0).
- `bug_type` — the category name, **even on safe cases** (see *How scoring
  works*): `SQL Injection`, `Cross-Site Scripting`, `Command Injection`,
  `Path Traversal`, `Weak Hash`, `LDAP Injection`.
- `cwe` — `CWE-89` or `89` are both accepted. **Fill it in on safe cases too**,
  using the category's CWE.
- `confidence` — a number from `0.0` to `1.0` (not scored, but recorded).

Place your files under a single folder named after your team or model:

```text
submissions/
  <your_team_or_model_name>/
    BenchmarkTest00027.json
    BenchmarkTest00294.json
    ...
```

## Recording your run

In **every** JSON, fill in `model_or_agent`, `model_name`, and
`web_search_used` accurately. Additionally, note in your submission folder (a
short `notes.md` is fine) the **human intervention level**, e.g.:

- *none* — the agent produced the JSON unaided,
- *light* — a human fixed formatting / re-prompted,
- *heavy* — a human materially changed the verdict or reasoning.

## Checklist before submitting

- [ ] One JSON per case, named `BenchmarkTestNNNNN.json`.
- [ ] `test_name` matches the file.
- [ ] `is_vulnerable` is `true`/`false` (not `null`).
- [ ] `cwe` and `bug_type` filled in **on every case, including safe ones**.
- [ ] `model_or_agent`, `model_name`, `web_search_used` recorded.
- [ ] Human intervention level noted.
- [ ] Closed-book run: web search off, only this repo's files were read.
