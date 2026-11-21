# agentest GitHub Action

[![self-test](https://github.com/srpadmanabhuni/agentest-action/actions/workflows/self-test.yml/badge.svg)](https://github.com/srpadmanabhuni/agentest-action/actions/workflows/self-test.yml) [![PyPI](https://img.shields.io/pypi/v/agentest-ai.svg)](https://pypi.org/project/agentest-ai/) [![tested with agentest](https://img.shields.io/badge/tested%20with-agentest-2ea44f)](https://github.com/srpadmanabhuni/agentest)

Run [agentest](https://github.com/srpadmanabhuni/agentest) agentic evals on your AI agent's OpenTelemetry traces in CI. The action installs `agentest-ai` from PyPI and runs 165 tests on what the agent actually did, such as unearned success claims, followed prompt injections, unapproved irreversible actions and leaked personal data. It runs offline, with no model calls, no API keys and no secrets.

## Usage

```yaml
name: agent evals
on: [pull_request]
jobs:
  agentest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: srpadmanabhuni/agentest-action@v1
        with:
          otlp: traces/*.otlp.jsonl
```

The step fails when any check returns `fail`, and the run totals and the Markdown report go to the job summary. To report without failing the build, set `fail-on-fail: "false"`.

## Inputs

| Input | Default | What it does |
|---|---|---|
| `otlp` | | OTLP trace files or globs (OTLP/JSON `.json`/`.jsonl`, OTLP/protobuf `.pb`), separated by spaces or new lines |
| `card` | | Optional A2A AgentCard JSON (with `otlp`) |
| `tools` | | Optional MCP `tools/list` JSON (with `otlp`) |
| `agents` / `agent` | `agents` | Without `otlp`: a directory of agent folders, and optionally one folder ID |
| `ids` | | Limit to test IDs or classes, for example `T5,T6-002` |
| `mode` | | `strict` or `examples` |
| `config` | | Optional YAML/JSON config file |
| `format` | `md` | `md`, `json`, `junit`, `sarif`, `html` or `csv` |
| `report` | | Report path (default `agentest-report.<ext>`) |
| `fail-on-fail` | `true` | Fail the step on any fail verdict |
| `job-summary` | `true` | Write totals (and the md report) to the job summary |
| `python-version` | `3.12` | Python to set up (3.11 or later); empty uses the runner's Python |
| `version` | | `agentest-ai` version from PyPI, for example `3.5.2`; empty means latest |
| `install` | | Advanced: any pip requirement instead of the PyPI release |

Outputs: `exit-code`, `passed`, `failed`, `not-applicable`, `report`.

### Upload SARIF to code scanning

```yaml
      - uses: srpadmanabhuni/agentest-action@v1
        with:
          otlp: traces/*.otlp.jsonl
          format: sarif
          fail-on-fail: "false"
      - uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: agentest-report.sarif
```

## Run it locally first

Install from PyPI:

```bash
pip install agentest-ai
```

Then try it:

```bash
agentest demo
agentest std run --otlp your-trace.jsonl --format md
```

## Learn more

- [agentest on GitHub](https://github.com/srpadmanabhuni/agentest): source, docs and issues
- [agentest-ai on PyPI](https://pypi.org/project/agentest-ai/)
- [Tutorials](https://github.com/srpadmanabhuni/agentest/tree/main/docs/tutorials), including [CI/CD with GitHub Actions](https://github.com/srpadmanabhuni/agentest/blob/main/docs/tutorials/04-ci-cd-github-actions.md)
- [Wall of caught failures](https://github.com/srpadmanabhuni/agentest/blob/main/docs/WALL-OF-CAUGHT-FAILURES.md)
- ["Tested with agentest" badge](https://github.com/srpadmanabhuni/agentest/blob/main/docs/TESTED-WITH-AGENTEST.md)

Example traces in `examples/` come from the agentest repository. Apache-2.0.
