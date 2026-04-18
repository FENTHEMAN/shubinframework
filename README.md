# Shubin Framework

*Context-first markdown framework for small teams working with AI agents.*

**Languages:** English (this file) · [Русский](./README.ru.md)

---

## What this is

A set of practices for treating a markdown vault as **shared team context** for both humans and AI agents (Claude Code, Cowork).

When a team works with AI agents in daily engineering, two pains appear:

- **AI agent amnesia** — every session starts from scratch, decisions made yesterday need re-explaining tomorrow.
- **Human context loss** — three months later nobody remembers why the API returns camelCase, or why onboarding has 4 steps and not 1.

Both are the same problem: *team context lives in people's heads, not in a readable form.*

Shubin Framework puts the context into one markdown repository (the vault), read equally by humans and AI agents. Plus an optional **knowledge module** implementing Andrej Karpathy's LLM-wiki method, where the LLM actively maintains compiled research pages from raw sources.

## Who this is for

- Teams of 2–8 building a product from MVP to early traction
- Using AI agents (Claude Code, Cowork) as regular team members
- With at least one person willing to push the practices (tech lead, founder)

**Not for:** teams of 15+, teams without a discipline driver, flow-of-clients agencies, or pure solo work that would be overloaded by the full version (see the lite version).

## Quick start

Pick the document matching your situation:

| Your situation | Start with |
|---|---|
| Solo or pair on MVP / pet project | [`lite.md`](./lite.md) |
| Tech lead of a 3–8 team, setting up from scratch | [`intro.md`](./intro.md) → [`framework.md`](./framework.md) |
| PM / designer / CEO on a team that already adopted the framework | [`non-tech.md`](./non-tech.md) |
| Developer team starting to accumulate user research / competitive intel | [`knowledge.md`](./knowledge.md) |
| Founder facing first enterprise customer or security questions | [`non-engineering.md`](./non-engineering.md) |

Don't read all of them at once. Each document is meant to be read against a concrete task.

## Vault concept

```
vault/
├── stable/              rarely changes (stack, team, glossary)
├── active/              main work (features, decisions/ADRs, patterns)
├── log/                 append-only (feedback, incidents, retros)
└── knowledge/           optional Karpathy LLM-wiki module
    ├── raw/             immutable external sources
    ├── wiki/            LLM-compiled pages with cross-refs
    └── CLAUDE.md        LLM maintainer instructions
```

Two principles the whole framework rests on:

1. **Append-only for history, mutable for state.** Decisions, feedback, incidents — only appended. Roadmap, feature pages, ADR statuses — change.
2. **One source of truth per fact.** Each fact has exactly one canonical home. Duplicates diverge.

Everything else is a consequence.

## Relationship to Karpathy's method

In April 2026, Andrej Karpathy published the "LLM wiki" pattern: a three-layer knowledge base (raw immutable sources → LLM-compiled wiki with cross-references → CLAUDE.md schema) where the LLM is the maintainer.

Shubin Framework's core (chapters 2–11 of `framework.md`) is classical engineering practice: ADRs, feature specs, patterns, runbooks — humans write, LLM reads. Karpathy's method is implemented as a separate optional module (`knowledge.md`), where the LLM becomes the maintainer of the wiki. See that document for the full mechanics.

Original Karpathy gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## Documents

All documents on this level are in English. Russian translations are in [`i18n/ru/`](./i18n/ru/).

- [`intro.md`](./intro.md) — 10-minute hub for navigating the framework
- [`framework.md`](./framework.md) — main document, for tech leads
- [`lite.md`](./lite.md) — for solo and 1–2 person teams on MVP
- [`knowledge.md`](./knowledge.md) — Karpathy LLM-wiki module for team knowledge management
- [`non-tech.md`](./non-tech.md) — git and vault without the terminal
- [`non-engineering.md`](./non-engineering.md) — minimum on Finance/Legal/Security/Data/Support

## Conventions

Project conventions for contributors and for teams adopting the framework:

- [`conventions/GIT_CONVENTIONS.md`](./conventions/GIT_CONVENTIONS.md) — git commit and branch conventions

## Honest warnings

The framework pays off in months 2–3, not immediately. Without a person pushing the practices, it dies within a quarter. Not everything here fits every team — adapt, don't copy.

If you set this up and after 3 months the vault isn't being used, that's usually not a framework failure but a signal of a deeper problem: the team lacks clarity on what it's building. No tool replaces that.

## License

MIT. See [LICENSE](./LICENSE).

## Contributing

This is a first public version. Feedback, counterexamples, and reports of practices that didn't work for your team are especially welcome — the framework adapts, it's not a truth.
