# andersonfpcorrea-skills

A private Claude Code skill marketplace. Currently ships one plugin, **`book-skills`** — a set of knowledge-base skills extracted from engineering books.

> **Study aids, not substitutes.** Each skill is a condensed set of structural notes — frameworks, principles, and anti-patterns — distilled from the named book. They summarize ideas and methods rather than reproduce the text, and are meant to complement the original work, not replace it. If a book is useful to you, buy it and read it.

## What's inside

The `book-skills` plugin bundles nine on-demand knowledge bases. Each one loads ~3K tokens of core frameworks when invoked and reads individual chapter files only when a question maps to them.

| Skill | Book | Use it for |
|---|---|---|
| `huyen-ai-engineering` | AI Engineering — Chip Huyen | Foundation models, RAG, evaluation, finetuning, inference optimization |
| `observability-engineering` | Observability Engineering — Majors, Fong-Jones, Miranda | Structured events, tracing, OpenTelemetry, core analysis loop |
| `ousterhout-software-design` | A Philosophy of Software Design — Ousterhout | Deep modules, complexity, the 15 principles + 14 red flags |
| `google-sre` | Site Reliability Engineering (Google) | SRE philosophy, toil, on-call, postmortems, cascading failures |
| `sre-workbook` | The Site Reliability Workbook (Google) | Practical SLOs, multi-burn-rate alerting, error-budget policy, canarying |
| `secure-reliable-systems` | Building Secure and Reliable Systems (Google) | Security × reliability, least privilege, resilience, recovery, crisis mgmt |
| `hidalgo-slo` | Implementing Service Level Objectives — Hidalgo | Deepest SLI/SLO/error-budget treatment + the statistics behind them |
| `accelerate-devops` | Accelerate — Forsgren, Humble, Kim | DORA four key metrics, 24 capabilities, Westrum culture |
| `dynamodb-book` | The DynamoDB Book — DeBrie | Single-table design, modeling strategies, access patterns |

## Install

```
/plugin marketplace add git@github.com:andersonfpcorrea/andersonfpcorrea-skills.git
/plugin install book-skills
```

Update later from `/plugin`.

## Use

Skills auto-trigger on relevant work, or invoke explicitly:

- `huyen-ai-engineering RAG` — load the skill and jump to a topic
- "using hidalgo-slo, how do I pick a reliability target?" — natural-language invocation
- "what chapters do you have?" — browse a skill's index

## How these were built

Generated with the [`book-to-skill`](https://github.com/andersonfpcorrea/book-to-skill) converter, which extracts a book's frameworks, mental models, principles, techniques, and anti-patterns into the structured skill layout (`SKILL.md` + `chapters/` + `glossary.md` + `patterns.md` + `cheatsheet.md`).

## Layout

```
andersonfpcorrea-skills/
├── .claude-plugin/
│   └── marketplace.json        # marketplace manifest
├── plugins/
│   └── book-skills/
│       ├── .claude-plugin/
│       │   └── plugin.json      # plugin manifest
│       └── skills/              # one directory per skill
└── README.md
```
