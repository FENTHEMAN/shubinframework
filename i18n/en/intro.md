# Shubin Framework: Intro

**Full title:** Context-First Framework for small teams with AI — hub
**Version:** 1.0
**Reading time:** 10 minutes

This document is the entry point to Shubin Framework. If you're hearing about the framework for the first time, start here. If you've already read the main document, you can skip this.

---

## 1. What it is

Shubin Framework is a set of practices for treating a markdown vault as **shared team context** for both humans and AI agents (Claude Code, Cowork).

Core idea: **team context should live in a form that humans and AI can both read, in git, not in someone's head**.

When the practices actually work, the team gets:
- Claude Code stops re-opening rejected decisions every session
- New team members onboard noticeably faster than through verbal walkthroughs
- A key person leaving is a serious pain, but not a catastrophe
- The team stops losing ten minutes on every "wait, why did we do it this way?"

When it doesn't work:
- The team writes to the vault, but nobody reads it
- Tech lead doesn't push the practices
- Documents get updated as formality, disconnected from actual decisions

**Without discipline, nothing works.** This isn't "set it and forget it" — it's a long-term practice that pays off in months 2–3 if used genuinely.

---

## 2. Where it comes from

The framework is based on two sources:

**Andrej Karpathy's "LLM wiki" method** — a personal knowledge management pattern where the LLM maintains a markdown wiki: compiling raw sources into structured pages, keeping cross-references up to date. In Shubin Framework this method is implemented as an **optional module** (`knowledge/`), applicable to team research and knowledge management.

**Classical engineering practice** — ADRs (Architecture Decision Records, Michael Nygard), feature specs, runbooks, event taxonomy. This is the core of most chapters of the framework.

The combination: core engineering discipline + optional LLM wiki for domain knowledge.

---

## 3. Who it's for and who it isn't

**Who it's for:**
- Teams of 2–8 people, from MVP to early traction
- Using AI agents (Claude Code, Cowork) in daily work
- Having at least one person willing to **push the practices** (tech lead, founder)
- Building a product where accumulated context pays off (SaaS, B2B tools, complex B2C)

**Who it isn't for:**
- Teams of 15+ — you need heavier processes, enterprise practices
- Teams without a tech lead willing to review and nudge — will die within a quarter
- Pet projects and MVPs by a solo developer — see the lite version
- Agencies with a flow of client projects — different knowledge flow shape

If you're solo or in a team of 1–2, start with `lite.md`. The full version is overkill.

---

## 4. Document navigation

The framework consists of five documents. Each for a specific need.

### `framework.md` — **main document, for tech leads**
Everything needed to set up and maintain a vault for a team of 2–8. Vault structure, ADR process, feature iteration cycle, roles, development, design with Figma, infrastructure, testing, rituals, anti-patterns, phased rollout. Read this first if you're a tech lead.

### `lite.md` — **for solo and teams of 1–2 on MVP**
Simplified version: two files at start (`decisions.md`, `todo.md`), `CLAUDE.md` in the project root, 15 minutes of setup. No ADRs, no rituals, no roles. Plus — a simplified knowledge module for MVPs that accumulate external sources.

### `knowledge.md` — **LLM wiki module for team knowledge management**
Karpathy's method adapted for teams. Needed when you accumulate external sources (user interviews, competitor articles, research) and want the LLM to compile them into linked wiki pages automatically. Read by all roles — developers, PM, designers, CEO.

### `non-tech.md` — **for PM, designer, CEO without engineering background**
How to work with git and vault without the terminal. GitHub Desktop, Obsidian, four operations (clone/pull/commit/push), typical situations, what's allowed and what isn't. 45 minutes of setup.

### `non-engineering.md` — **minimum on Finance/Legal/Security/Data/Support**
Areas outside core engineering that still need attention. Minimal practices for teams of 2–8: what to do, what to defer until traction. Avoids both cargo-cult enterprise and "security later" disasters.

---

## 5. Quick "where to start" router

**I'm solo or a pair, building an MVP or pet project.**
→ Only `lite.md`. Nothing else needed.

**I'm a tech lead of a 3–8 team, setting up the process from scratch.**
→ `intro.md` (this one) → `framework.md` → others as needs arise.

**I'm a PM / designer / founder, the team has already adopted the framework, I need to work with the vault.**
→ Only `non-tech.md`. If the team uses the knowledge module — add section 8 there.

**We're a team of developers, starting to accumulate user research, competitive analysis.**
→ Add `knowledge.md` to the main framework. Read by all roles.

**I'm a founder, first enterprise customer or security questions approaching.**
→ `non-engineering.md`. This is the minimum. For serious compliance needs, you need specialists.

---

## 6. Vault concept overview

```
vault/ (single git repository)
│
├── stable/              rarely changes
│   ├── stack.md         tech stack
│   ├── team.md          who owns what
│   └── glossary.md      domain terms
│
├── active/              main work
│   ├── features/        each feature — a folder with spec and progress
│   ├── decisions/       ADRs (sequentially numbered)
│   └── patterns/        accumulated engineering patterns
│
├── log/                 append-only, not edited
│   ├── feedback/        user signals
│   ├── incidents/       postmortems
│   └── retrospectives/  retro summaries
│
├── knowledge/           ← optional module (Karpathy's method)
│   ├── raw/             immutable external sources
│   ├── wiki/            LLM-compiled pages with cross-refs
│   └── CLAUDE.md        LLM maintainer instructions
│
├── COMMON.md            schema for AI agents
├── README.md
└── index.md             catalog
```

Key differences between zones:

| Zone | Who writes | Who edits | Character |
|---|---|---|---|
| stable/ | humans | humans (rarely) | declarative |
| active/ | humans | humans | process |
| log/ | humans | nobody (append-only) | chronicle |
| knowledge/raw/ | humans | nobody (immutable) | sources |
| knowledge/wiki/ | LLM | LLM | compiled |

---

## 7. Two framework principles

**Principle 1: Append-only for history, mutable for state.**
Decision log, feedback, incidents — only added to. Roadmap, feature pages, ADR statuses — change. The folder structure directly reflects this principle: `stable/` and `active/` — mutable, `log/` — append-only, `knowledge/raw/` — immutable.

**Principle 2: One source of truth per fact.**
Each decision, each feature, each component has exactly one canonical home. Duplicates diverge. If something is described in Linear, Figma, and the vault — within a month you'll have three divergent descriptions.

All other practices are consequences of these two.

---

## 8. What to expect over time

- **First month:** setup, first feature through the full workflow. No visible payoff yet.
- **Months 2–3:** rituals become habits, first returns appear (Claude Code stops re-opening decisions, team starts referencing ADRs).
- **Months 4–6:** compounding — accumulated context creates real acceleration. New person onboards in 1–2 weeks. Team velocity higher than before the framework.
- **After 6 months:** expansion as needed (enterprise clients, compliance, scaling).

If after 3 months the vault isn't being used, that's not a framework failure, it's a signal of a deeper problem: the team lacks clarity on what you're building and why. No tool replaces that clarity.

---

## 9. Critical anti-patterns

Read these in detail in the main document's "Anti-patterns" chapter. But the most common ones:

1. **Vault as write-only medium** — people write, nobody reads
2. **Single writer** — only the tech lead actually maintains it
3. **AI generates, nobody reviews** — half the ADRs contain hallucinated trade-offs
4. **Ritual theater** — meetings happen, decisions don't

Common antidote: **tech lead discipline + visible vault references in daily work (PRs, discussions)**. Without this, the framework is a graveyard of documents.

---

## 10. Next step

Use section 5 to pick which document to read next. Usually one of:
- `lite.md` (if solo/MVP)
- `framework.md` (if tech lead of a 3–8 team)
- `non-tech.md` (if you're a non-developer on a team that's already adopted the framework)

Don't try to read everything at once. Documents are read against a concrete task: "setting up the team vault", "don't know git, need to contribute", "connecting the knowledge module". Read against a task.

---

*The framework is a scaffold, not a truth. Adapt it to your team. The core is two principles and discipline.*
