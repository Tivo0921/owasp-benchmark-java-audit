# OWASP BenchmarkJava — AI Code-Audit Challenge (100 cases)

A static **code-audit** challenge for AI coding agents. Each file under `cases/`
is a single Java testcase taken from the
[OWASP Benchmark](https://owasp.org/www-project-benchmark/) project. For each
case, read the code and decide whether it contains a **real vulnerability**, then
produce a structured JSON report.

This is **not** a capture-the-flag or exploitation challenge — no flag, no
exploit, no running the app. You only read the source and reason about it. Some
cases are genuinely vulnerable; others are deliberate **false positives** that
look risky but are safe. Telling them apart is the point.

> **Read the dependencies.** The cases reference shared code in `helpers/`
> (package `org.owasp.benchmark.helpers`) and config in `benchmark.properties`
> by fully-qualified name. The verdict often depends on them (e.g. which hash
> algorithm `benchmark.properties` selects, or whether a `helpers` method is a
> safe constant or a tainted source). See `participant-guide.md →
> Repository contents`.

## What you do

For every `cases/BenchmarkTestNNNNN.java`, produce one JSON file named after the
test (e.g. `BenchmarkTest00027.json`) following `report_template.json`:

```json
{
  "test_name": "BenchmarkTest00027",
  "model_or_agent": "your-agent",
  "model_name": "your-model",
  "web_search_used": false,
  "is_vulnerable": true,
  "bug_type": "SQL Injection",
  "cwe": "CWE-89",
  "vulnerable_file": "BenchmarkTest00027.java",
  "vulnerable_function": "doPost",
  "root_cause": "…",
  "security_impact": "…",
  "patch_strategy": "…",
  "confidence": 0.9
}
```

Put your files under one folder named after your team/model:

```text
submissions/<your_team_or_model_name>/BenchmarkTest00027.json
```

See [`participant-guide.md`](participant-guide.md) for the full rules
(closed-book by default: **no web search** unless the organizer enables it).

## Closed-book enforcement

The OWASP Benchmark answer key is **publicly available online**, so a
closed-book result is only meaningful if the agent does not look anything up
**during the run**. This repo ships [`.claude/settings.json`](.claude/settings.json)
that denies `WebSearch`/`WebFetch` for Claude Code. Run the agent with **only
this repository** in context, web/browse tools off. See *participant-guide.md →
Web search rule* for details and the equivalent for other agents.

## Scoring

3 points per case: `is_vulnerable` (1) + `cwe` (1) + `bug_type`/category (1).

**Every case has a category even when the code is safe.** For a case you judge
safe, set `is_vulnerable: false` **and still** fill `bug_type`/`cwe` with the
category the code is about — otherwise you lose those 2 points. See
*participant-guide.md → How scoring works*.

### Self-grading

The grading harness and answer key are in the organizer repository:
**[owasp-benchmark-audit-organizer](https://github.com/Tivo0921/owasp-benchmark-audit-organizer)**.
After you finish (closed-book!), you can score yourself:

```bash
# in the organizer repo
python scripts/grade_submissions.py --submissions /path/to/your/submissions
```

## Categories in this set

SQL Injection (CWE-89), Cross-Site Scripting (CWE-79), Command Injection
(CWE-78), Path Traversal (CWE-22), Weak Hash (CWE-328), LDAP Injection (CWE-90).
100 cases, balanced across categories, mixing true vulnerabilities and false
positives.

## Attribution & License

The Java testcases in `cases/` are part of the **OWASP Benchmark Project**
(© Dave Wichers / OWASP) and are redistributed **unmodified** under the
**GNU General Public License v2.0**. The original copyright and license headers
are preserved in every file. The full license text is in [`LICENSE`](LICENSE).

- Upstream: https://github.com/OWASP-Benchmark/BenchmarkJava
- Project: https://owasp.org/www-project-benchmark/

Because this repository redistributes GPL-2.0 source, the repository is
distributed under **GPL-2.0**. If you add your own code (e.g. tooling), keep the
OWASP testcases and their headers intact and do not relicense them.

> This README is informational, not legal advice.
