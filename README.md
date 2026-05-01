# Shubin Framework

*Context-first markdown framework for small teams working with AI agents.*

**Languages:** English (this file) · [Русский](./README.ru.md)

---

## What this is

A set of practices for treating a single markdown repository as **shared team context** — read equally by humans and AI agents (Claude Code, Cowork). The repository holds the spec; GitHub holds the state. Markdown carries decisions, feature specs, and patterns that don't change every day. Issues, milestones, and PR timelines carry status, ownership, priority, and progress.

Two pains show up together in small teams that work with AI agents day to day. Sessions start from scratch and yesterday's decisions need re-explaining tomorrow. Three months in nobody remembers why the API returns camelCase or why onboarding has four steps and not one. Both are the same problem: context lives in people's heads. Shubin Framework moves it into one place that both humans and agents can read.

The core is classical engineering practice — ADRs, feature specs, patterns, runbooks, a journal of feedback and incidents. An optional `knowledge/` module implements Andrej Karpathy's "LLM wiki" method, where the LLM maintains compiled research pages from raw sources. The two are independent; adopt the core without the module, or both.

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
knowledge.md           Karpathy LLM-wiki module
non-tech.md            git and the repository without the terminal
non-engineering.md     Finance / Legal / Security / Data / Support minimum

guides/                long-form examples (e.g. feature cycle walkthrough)
conventions/           cross-cutting conventions for contributors
i18n/ru/               Russian translations
```

The lower-case files are the framework documents an adopting team reads. The UPPER_CASE files are meta-documents about this repository. `ARCHITECTURE.md` covers the layout and invariants in more detail.

## Where to start

Pick the document matching your situation. Don't read all of them at once — each is meant to be read against a concrete task.

| Your situation | Start with |
|---|---|
| Solo or 1–2 person team on MVP | [`lite.md`](./lite.md) |
| Tech lead of a 3–8 team setting up from scratch | [`framework.md`](./framework.md) |
| PM, designer, or CEO joining a team that already adopted it | [`non-tech.md`](./non-tech.md) |
| Team accumulating user research or competitive intel | [`knowledge.md`](./knowledge.md) |
| Founder facing the first enterprise customer | [`non-engineering.md`](./non-engineering.md) |

## Honest warnings

The framework pays off in months two and three, not on day one. Without one person actually pushing the practices it dies within a quarter. Adapt rather than copy — none of this is gospel, and a repository that isn't being read after three months is usually a signal of a deeper problem (the team lacks clarity on what it is building) that no tool replaces.

## License and contributing

MIT — see [LICENSE](./LICENSE). Counterexamples, real-world cases, and reports of practices that didn't work for your team are especially welcome; see [CONTRIBUTING.md](./CONTRIBUTING.md).
