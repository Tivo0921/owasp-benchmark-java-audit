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

## Scoring

The organizer grades each case against the private OWASP Benchmark answer key
(`expectedresults-1.2.csv`, **not included here**). 3 points per case:
`is_vulnerable` (1) + `cwe` (1) + `bug_type`/category (1).

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
