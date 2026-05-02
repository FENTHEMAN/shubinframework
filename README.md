# Shubin Framework

*Context-first markdown framework for small teams working with AI agents.*

**Languages:** English (this file) · [Русский](./README.ru.md)

---

## What this is

Two stubborn problems show up in any small team using AI agents: agents have no memory across sessions, and humans drift out of context as the project grows. Shubin Framework is a set of conventions for solving both with three coordinated layers, no custom tooling required.

The first layer is a **markdown repository** holding durable knowledge: decisions (ADRs), patterns, architecture, glossary, a journal of feedback and incidents, and an optional AI-compiled domain wiki. Things you'd want a new hire — or a new agent — to read on day one. The second layer is **GitHub Issues, Milestones, and Projects v2**, where the live workflow happens: features, sub-issues, cross-repo dependencies, sprint cadence, roadmap. The third is an **AI agent** that periodically compiles the wiki from sources — both the manually curated `knowledge/raw/` and the team's actual GitHub workflow (closed Issues, merged PRs, new ADRs, journal entries).

Markdown holds the spec. GitHub holds the state. The agent compiles the bridge. The conventions are read by humans and agents equally.

This is documentation, not a tool. Adopt the layout and the rules; the framework becomes whatever your team actually does with it.

## Who this is for

Teams of two to eight people, building a product from MVP to early traction, using AI agents as regular contributors, with at least one person willing to push the practices day to day (tech lead or founder).

**Not for:** teams of fifteen or more (you need heavier process), teams without a discipline driver (the repository will go write-only and die within a quarter), agencies with a flow of client projects, or pure solo work that the full version would overload — the lite variant exists for that.

## Repository layout

```
README.md              English entry point
README.ru.md           Russian entry point
ARCHITECTURE.md        map of this repo for contributors
LICENSE                MIT
CONTRIBUTING.md        how to propose changes
CHANGELOG.md           release history

framework.md           full specification, for tech leads
lite.md                solo and 1–2 person variant
knowledge.md           Karpathy LLM-wiki module + auto-ingest spec
projects.md            GitHub Projects v2 integration spec
non-tech.md            git and the repository without the terminal
non-engineering.md     Finance / Legal / Security / Data / Support minimum

decisions/             ADRs (immutable, sequentially numbered)        — adopting team
patterns/              engineering patterns                            — adopting team
journal/               feedback, incidents, retros (append-only)       — adopting team
knowledge/             raw sources + AI-compiled wiki                  — adopting team

guides/                long-form examples (e.g. feature cycle walkthrough)
conventions/           cross-cutting conventions for contributors
templates/             files an adopting team copies (Issue Templates, knowledge/AGENTS.md)
i18n/ru/               Russian translations
```

The lower-case files are the framework documents an adopting team reads. The UPPER_CASE files are meta-documents about this repository. `ARCHITECTURE.md` covers the layout and invariants in more detail.

## Where to start

Pick the document matching your situation. Don't read all of them at once — each is meant to be read against a concrete task.

| Your situation | Start with |
|---|---|
| Solo or 1–2 person team on MVP | [`lite.md`](./lite.md) |
| Tech lead of a 3–8 team setting up from scratch | [`framework.md`](./framework.md) |
| Setting up GitHub Projects v2 for cross-repo planning | [`projects.md`](./projects.md) |
| Adding the AI knowledge wiki (Karpathy module + auto-ingest) | [`knowledge.md`](./knowledge.md) |
| PM, designer, or CEO joining a team that already adopted it | [`non-tech.md`](./non-tech.md) |
| Founder facing the first enterprise customer | [`non-engineering.md`](./non-engineering.md) |

## Honest warnings

The framework pays off in months two and three, not on day one. Without one person actually pushing the practices it dies within a quarter. Adapt rather than copy — none of this is gospel, and a repository that isn't being read after three months is usually a signal of a deeper problem (the team lacks clarity on what it is building) that no tool replaces.

## License and contributing

MIT — see [LICENSE](./LICENSE). Counterexamples, real-world cases, and reports of practices that didn't work for your team are especially welcome; see [CONTRIBUTING.md](./CONTRIBUTING.md).
