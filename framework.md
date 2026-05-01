# Shubin Framework

**Full title:** Context-First Framework for small teams with AI
**Author:** Fen
**Version:** 5.0
**Date:** 2026-05-02

**Who it's for:** teams of 2–8 building a product from MVP to early traction, using AI agents (Claude Code, Cowork) as regular team members.

**What it is:** a scaffolding of practices that pays off in months 2–3 of use, given discipline.

**What it isn't:** an out-of-the-box recipe, a replacement for engineering taste, a silver bullet, a ready-made enterprise playbook.

---

## Place in the document family

This is the main document, for the team's tech lead. The companions cover specific needs:

- `lite.md` — solo developers and teams of 1–2 on MVP
- `knowledge.md` — LLM wiki module following Karpathy's method
- `non-tech.md` — working with the repository and git without the terminal (PM, designer, CEO)
- `non-engineering.md` — minimum on Finance, Legal, Security, Data, Support for a 2–8 team
- `guides/feature-cycle-example.md` — full worked example of the feature cycle in chapter 5

This file covers repository architecture, the ADR process, the feature iteration cycle, development, infrastructure, and rituals. For a sub-topic, see the corresponding companion.

---

## Before starting: three honest warnings

**1. The framework doesn't pay off immediately.** In the first month you spend time on setup with no visible win. In months 2–3 it starts paying off. By month 6 the team moves faster than without the framework. If you are not ready for this lag — don't start.

**2. Without discipline, nothing works.** A repository with no readers is a graveyard. ADRs without review are a formality. Rituals without consequences are theater. None of this is "set and forget".

**3. Not all of this is for you.** The document describes a mature state. You start with the minimum and expand as you grow. Expand only when you actually hit the absence of a practice, not when "the framework says so".

---

## Contents

1. Core idea
2. Repository structure
3. Scaling: when one repo isn't enough
4. Unit of work: Feature + ADR
5. Feature iteration cycle
6. Roles and AI agents
7. Development
8. Design process
9. Infrastructure and deploy
10. Testing
11. Rituals
12. Anti-patterns
13. Phased rollout

---

## 1. Core idea

### 1.1. The problem

A small team with AI agents hits two pains:

- **AI agent amnesia.** Every Claude Code session starts from scratch. Decisions made yesterday need re-explaining tomorrow.
- **Human context loss.** Three months later nobody remembers why the API returns camelCase, why this particular DB was chosen, why onboarding has four steps instead of one.

Both problems are the same one: **context lives in people's heads, not in a readable form**.

### 1.2. The solution

Move team context into **a single markdown repository**, read equally by humans and AI agents. Everything important is written there. Anything that isn't written doesn't exist for new joiners or for Claude Code.

Advantages of markdown-in-git over Notion or Confluence: plaintext search and grep work; AI agents read and edit natively; versioning out of the box; no vendor lock-in; no integrations and no API rate limits.

### 1.3. The two principles behind everything

**Principle 1: Append-only for history, mutable for state.** Decision log, feedback, incidents — only appended. Roadmap, feature pages, ADR statuses — change. This separation gives honest history without chaos.

A v2 sharpening of this principle: **mutable state lives in GitHub, not in markdown frontmatter**. Status, ownership, priority, due dates, progress timelines — these are GitHub Issues, Milestones, and PR timelines. Markdown holds the *spec*; GitHub holds *where it is now*. The implications run through chapters 4 and 5.

**Principle 2: One source of truth per fact.** Each decision, each feature, each component has exactly one canonical home. Duplicates diverge. If something is described in Linear, Figma, and the repository, within a month you have three divergent descriptions.

There are no other principles. Everything else is a consequence.

### 1.4. Relationship to Karpathy's method

The core of the repository (chapters 2–12) is classical engineering practice: ADRs, feature specs, patterns, runbooks. Humans write, the LLM reads.

A separate **optional module** `knowledge/` implements Andrej Karpathy's "LLM wiki" method: humans drop raw sources (interviews, articles, research), the LLM compiles them into linked wiki pages (personas, concepts, competitors). This is the only layer where the LLM is the maintainer.

The module lives in a separate document: `knowledge.md`. Connect it if your team does a lot of discovery work and wants shared synthesized memory about the external world. Before that — don't.

---

## 2. Repository structure

### 2.1. Minimum structure to start

Don't create twelve folders on day one. Start with the canon files in root and three working directories plus the journal:

```
your-product/
├── ARCHITECTURE.md       map of the system
├── STACK.md              tech stack
├── GLOSSARY.md           domain term glossary
├── TEAM.md               who owns what (optional)
├── AGENTS.md             instructions for AI agents
├── README.md
│
├── decisions/            ADRs, sequentially numbered
├── features/             one folder per feature
├── patterns/             accumulated engineering patterns
│
└── journal/              append-only
    ├── feedback/         dated raw feedback notes
    ├── incidents/        postmortems
    └── retros/           weekly and quarterly retro summaries
```

Optional: `knowledge/` (LLM wiki module, see `knowledge.md`); `guides/` (long-form examples and team-specific recipes); `runbooks/` (incident playbooks).

Folders are added when actually needed. `runbooks/` appears after the first incident. `guides/` appears when you have a worked example longer than two pages. Not earlier.

### 2.2. Why this layout

Three deliberate choices.

**Flat root over nested wrappers.** No `vault/` or any other catch-all directory. The repository itself is the wrapper. A redundant prefix on every path adds noise without information; tools like `grep` and `git log` work cleaner when the structure is flat.

**UPPER_CASE for canon, lower_case for working directories.** A contributor scanning the root should be able to spot meta-documents (`ARCHITECTURE.md`, `STACK.md`, `GLOSSARY.md`, `AGENTS.md`) without reading filenames carefully. Working directories — `decisions/`, `features/`, `patterns/`, `journal/` — are lowercase because they are *contents*, not canon.

**Change-mode classification over domain classification.** `journal/` is named for behavior (an append-only chronicle), not for content type. The alternative — `product/`, `engineering/`, `design/` — associates with org roles and provokes silos: "that's from engineering, I don't touch it". The change-mode scheme reflects Principle 1 directly.

If your team comes from an established Notion space with domain naming, an alternative layout is acceptable. The main thing: pick one and don't mix. Changing structure six months in is a painful migration.

### 2.3. What lives where

**Root canon files** rarely change. `ARCHITECTURE.md` — a high-level map of the product, bird's-eye view, module boundaries, invariants; matklad-style, prose-first, kept under ~300 lines. `STACK.md` — chosen tech stack with one-line reasons. `GLOSSARY.md` — domain terms shared by the whole team; module-specific terms go in module glossaries (chapter 3). `TEAM.md` — who owns what (optional in a 2–3 person team). `AGENTS.md` — rules for AI agents (see 2.5).

**Working directories** change actively. `decisions/` — ADRs, sequentially numbered (`0001-...md`); numbering never resets, no subfolders. `features/` — one folder per feature, `features/<slug>/index.md` plus optional supporting files; stateless specs (chapter 4.1). `patterns/` — engineering code patterns and gotchas, born from real recurrence (chapter 7.4).

**Journal** is append-only — entries are dated and never edited. `journal/feedback/` carries files like `2026-04-18-user-X-interview.md`; `journal/incidents/` carries postmortems; `journal/retros/` carries weekly and quarterly retro summaries.

Domain knowledge about the external world (personas, competitors, concepts) does not live in `GLOSSARY.md`. It either accumulates in plain markdown if the team doesn't use the knowledge module, or in `knowledge/wiki/` if it does.

### 2.4. Why not Notion

Fair question: what is different from a well-organized Notion workspace? Four things. AI agents read and write natively, no API or tokens, just file operations. Git versioning gives you `git log` and `git blame` decision archeology you cannot reproduce in Notion. Grep and ripgrep work instantly, including from CLI for agents. No vendor lock-in — moving to another platform is `git clone`.

Notion MCP exists, but markdown-in-git is faster, cheaper, and more reliable for a team with Claude Code on every machine.

### 2.5. AGENTS.md as the schema

A single file at the repository root describing rules for AI agents. `AGENTS.md` is the cross-tool standard adopted by Claude Code, Cowork, and others — we use that name rather than invent our own.

Minimal example:

```markdown
# Repository schema

## Structure

- decisions/ — ADRs, immutable after Accepted
- features/ — feature specs, stateless (state in linked GitHub Issues)
- patterns/ — engineering patterns, mutable
- journal/ — append-only history, never edit
- knowledge/ — optional LLM wiki module (see knowledge/AGENTS.md)

## When writing code

1. Read features/<current-feature>/index.md
2. Check decisions/ for relevant ADRs
3. Check patterns/ for similar patterns
4. On a new significant decision — propose creating an ADR

## On a significant finding

If you discover a gotcha, pattern, or decision worth preserving —
ask the user whether to record it in patterns/.
Don't create files silently.

## What not to do

- Don't edit journal/ (append-only)
- Don't add status/owner/priority frontmatter to features/ — that lives in GitHub Issues
- Don't create empty folders "for the future"
- Don't work with knowledge/ under general rules — it has its own AGENTS.md
```

`AGENTS.md` grows as the team learns. Honestly: in the first month it is short and updated often. By month 3 it stabilizes.

### 2.6. The repository as a separate repo

One markdown repository per product, independent of code. Other repositories attach it in one of three ways: git submodule (harder, but versions are pinned); git clone as a peer (easier, but everyone holds local copies); symlink after clone (so each code repo's `AGENTS.md` points to the canonical one).

The choice goes in the team's first ADR. Mixing methods within one team is bad — pick one and stick with it.

---

## 3. Scaling: when one repo isn't enough

### 3.1. Default: one repo per product

One product, one markdown repository with the structure above. This works as long as the team holds a single mental model of the product, any team member is usually aware of all major in-flight work, and features routinely cross domain boundaries.

Most 2–8 teams stay here forever. Don't optimize for scale you don't have.

### 3.2. Anti-pattern: split by tech layer

`backend-vault/`, `frontend-vault/`, `design-vault/` — **no**. Knowledge about a feature is integral: a transactions feature has a business invariant, a DB schema, an API, a UI, and a UX, all in one head. Splitting by tech layer creates silos and costs more than it saves.

### 3.3. Triggers for module split

Split into modules only when at least **two of three** are true:

1. **Team split.** Two teams with their own cycles and PMs — not "one person on backend, another on frontend".
2. **Release cadences diverge.** Modules deploy independently, on different schedules.
3. **Domain language is genuinely different.** Terms conflict across modules, or shared definitions in a single glossary become unwieldy.

One trigger alone is not enough. A single team with two release pipelines is still one team.

### 3.4. Hybrid: monorepo with `modules/`

When the triggers fire, keep one repository and split inside:

```
product/
├── ARCHITECTURE.md           map of the system and module boundaries
├── GLOSSARY.md               cross-module terms only
├── decisions/                cross-module ADRs
├── features/                 cross-module feature specs
├── modules/
│   ├── transactions/
│   │   ├── ARCHITECTURE.md   module internals
│   │   ├── GLOSSARY.md       authorize/capture/refund — only here
│   │   ├── decisions/
│   │   └── features/
│   └── notifications/
│       └── ...
```

One repository, two layers: the root carries cross-module context; each module carries its own.

### 3.5. Routing rule

When deciding where a document goes, ask three questions:

- If a brand-new module joined tomorrow, would it need this document? → root.
- If you renamed a module, would the document follow it? → module.
- If this module were spun out as its own product, would the document still belong inside? → module.

The questions are stricter than they look. Most documents fall into one bucket clearly.

### 3.6. Glossary: the strictest rule

Root `GLOSSARY.md` carries only terms used across modules. Domain-specific terms — `authorize`, `capture`, `refund` — go inside the module that owns them. Without this rule the root glossary swells past 200 terms in a year and nobody reads it.

### 3.7. Patterns: same rule

If a pattern recurs in three modules, lift it to root `patterns/`. Otherwise it lives in the module. Periodic refactoring of pattern locations is normal — patterns migrate as the system grows.

---

## 4. Unit of work: Feature + ADR

### 4.1. Feature as a stateless spec page

Each feature lives in `features/<slug>/index.md` as a **stateless specification**:

```markdown
# User Auth

**Tracking:** [#123](https://github.com/your-org/product/issues/123)

## Why

One sentence on why this is needed and for whom.

## Scope

What we're building. Boundaries: what we're NOT doing.

## Success criteria

Measurable check that the task is done.

## Decisions

- ADR-0015: auth strategy

## Design

Link to a Figma frame.
```

Notice what is **not** here: status, owner, priority, dates, implementation log, PR links. All of this is state, and state lives in GitHub:

- **Status, ownership, priority, dates** — fields on the tracking issue.
- **Progress timeline** — issue comments and the Linked PRs panel.
- **Decomposition into tasks** — sub-issues or task lists in the issue body.
- **Sprint or iteration** — the milestone the issue is in.

Markdown holds **what we are building and why**. GitHub holds **where it is now**.

This separation prevents the most common rot in markdown trackers: a feature page saying `status: in-progress` six months after the work shipped. There is exactly one source of truth for state (the issue) and exactly one for the spec (the markdown). One link — `**Tracking:**` — connects them.

### 4.2. ADR

Architectural Decision Record — a document about a decision that is hard to reverse, affects several parts of the system, and will need explaining three months from now.

A good boundary heuristic: if the decision can be reversed in a day of one person's work — no ADR needed. If the reversal is a week or more — ADR needed.

Format:

```markdown
# ADR-0015: <short decision title>

## Status

Accepted — 2026-04-17

## Context

What's the situation, what are the constraints.

## Options considered

Options with pros and cons. Even rejected ones are useful: six months later
you'll remember why you didn't go that way.

## Decision

Chosen option plus a sentence or two on why.

## Consequences

What we lose, what we gain, what becomes harder.

## Related

Links to features and services.
```

Numbering is sequential and never reset. Outdated ADRs get status `Deprecated` or `Superseded by ADR-NNNN`.

**ADRs do not get a GitHub Issue for tracking.** An ADR is a unit of *decision history*, not work-in-flight. Its `Status` field (`Accepted`, `Superseded by ADR-NNNN`) is part of the immutable document, not state to track elsewhere. This is the one exception to "state lives in GitHub" — and it is an exception because there is no state, only an immutable record.

If the team uses the knowledge module, an ADR can optionally include a **Research context** section linking to `knowledge/wiki/personas/` and `knowledge/wiki/competitors/` — this gives traceability to the actual interviews and research.

### 4.3. ADR-first — the ideal we aim for

Ideally a significant decision starts with a PR changing only the ADR. Discussion, approval, merge. Then code.

In practice, in a small team the tech lead often decides in their head and writes the ADR after some code is already written. That is fine. What matters is that the ADR eventually exists, even if written after the fact.

Danger: ADRs for ADRs' sake. Forty ADRs in the first quarter — half of them about HTTP client choice — and you have turned the practice into bikeshedding. A working norm is 5–15 ADRs in the first six months of active development.

---

## 5. Feature iteration cycle

This is where the repository, Figma, Claude Code, and GitHub converge into a single workflow. The full cycle from idea to production runs through eight steps. Each step has a specific artifact in a specific place.

For a worked example with concrete numbers, PRs, and quotes, see `guides/feature-cycle-example.md`.

### 5.1. Cycle overview

1. **Idea** — open a GitHub Issue and create a draft feature spec at the same time.
2. **Spec** — fill in Why, Scope, Success criteria in the markdown spec; the issue holds owner, priority, milestone.
3. **ADR (if needed)** — significant technical decisions get their own document under `decisions/`.
4. **Design** — Figma frames; link them from the spec's Design section.
5. **Implementation** — code PR(s) in the product repo, opened against the issue with `Closes #N`.
6. **Preview + review** — automatic preview environment per PR; designer and PM verify.
7. **Merge + deploy** — main branch deploys to production; PR merge auto-closes the issue.
8. **Feedback loop** — measurement two weeks after release; results land in `journal/feedback/`.

### 5.2. Step 1 — Idea

A new feature starts with two artifacts opened together. A GitHub Issue with a one-line title and a body that says, in two sentences, what the idea is and links to the spec page (`Spec: features/<slug>/index.md`). And a new folder `features/<slug>/` with `index.md` containing only the title, a `**Tracking:**` link, and a one-sentence Why.

The Issue carries the assignee, priority label, and milestone. The markdown carries the prose. They link to each other.

### 5.3. Step 2 — Spec

The same `index.md` is extended. PM and one developer (for technical realism) fill in Scope and Success criteria. The designer joins for UX-heavy features. When the PR with the spec is merged, the work is *validated* — but you don't write that word anywhere. The label on the issue (or its absence) is the source of truth for state.

### 5.4. Step 3 — ADR (if needed)

Triggered when the spec contains a non-trivial technical decision affecting more than one part of the system. The developer who'll implement it writes the ADR under `decisions/00NN-<slug>.md` and references it from the spec's `## Decisions` section.

### 5.5. Step 4 — Design

Designer (or developer with ready-made components) produces frames in the product's Figma project. The spec's `## Design` section gets a link to the Figma file or frame.

### 5.6. Step 5 — Implementation

The developer creates a branch in the code repository, runs Claude Code with the spec page, ADR, and Figma link as context, generates and reviews code, writes tests for critical logic. The code PR is opened with `Closes #N` so it auto-closes the tracking issue on merge.

The feature's PR links appear in the issue's "Linked pull requests" panel automatically — no manual editing of the spec page.

### 5.7. Step 6 — Preview and review

Per push, a preview environment is deployed automatically. The designer compares with Figma; the PM verifies scope. Discrepancies are PR comments. Usually 1–3 iterations.

### 5.8. Step 7 — Merge and deploy

Approve from one or two reviewers (depending on team size), merge to main, automatic deploy to production. The issue closes automatically via `Closes #N`. No manual status update in the spec page — there is no status in the spec page.

### 5.9. Step 8 — Feedback loop

Two weeks after release (per the success criteria from step 2), the PM or designer checks metrics in PostHog, creates `journal/feedback/2026-MM-DD-<slug>-results.md` with numbers, user quotes, and conclusions, and — if changes are needed — opens a new feature with a new issue.

If the team uses the knowledge module, significant insights from this feedback file flow into the next wiki ingest cycle.

### 5.10. When reality diverges from plan

Typical: at step 5 the developer realizes the approach chosen in the ADR doesn't work. Don't silently change the code and forget the ADR. Do: update the ADR's status to `Superseded by ADR-NNNN`, create the new ADR with the new decision and the reason for rejecting the old one, update `features/<slug>/index.md` if scope changed, and add a comment on the issue. Fifteen minutes. Six months later nobody scratches their head about why the code says one thing and the ADR says another.

---

## 6. Roles and AI agents

### 6.1. Base roles

In a 2–8 team, roles are functions, not titles. One person can carry 2–3 roles; what matters is each function being explicit.

| Function    | What it does                 | Owns in the repository                                 |
|-------------|------------------------------|--------------------------------------------------------|
| Product     | Holds "why we're doing this" | `features/*` (Why), `journal/feedback/`                |
| Design      | Visual and UX logic          | `features/*/design`                                    |
| Engineering | Code and architecture        | `patterns/`, `decisions/`, code repos                  |
| Ops         | Infra, deploy, incidents     | `runbooks/`, `infra/`                                  |

Specialized roles (DevOps, QA, Security, Data) are added on actual need, not by plan. The "it's time" signal is when the existing team steadily spends more than 20% of its time in that zone.

### 6.2. AI agents as part of the team

Each AI agent has a **configuration** that makes it a disciplined team member, not a general-purpose chatbot.

- **Claude Code in every code repo** — its own `AGENTS.md` referencing the repository's root `AGENTS.md`.
- **Cowork** — configured once on the machine of the person using it.
- **Claude Design** — optional and with care; only for one-off prototypes and presentations (see 8.1).

This isn't "six virtual job titles in a table". It is **one or two active agents per person per day**, configured to read the repository and follow its conventions.

### 6.3. AI delegation boundaries

AI agents do: generate code from a specification; read the repository and answer in context; write ADR drafts on request; help with routine changes; maintain the knowledge wiki when the module is used.

AI agents do not: make product decisions; act in production without human-in-the-loop; write ADRs without human review (you get hallucinated trade-offs); commit to main without a PR and review.

---

## 7. Development

### 7.1. Stack choice

The framework **doesn't dictate a stack**. The choice depends on team experience, product requirements, hiring in your region, runway budget. The main rule: write the team's first ADR about the stack. Whatever the choice, it must be argued and recorded.

**Pragmatic default for web CRUD:** TypeScript/Node.js. One language across the stack, mature ecosystem, easier hiring. A boring choice — usually the right one. Exceptions: heavy computation (ML serving, video and audio processing), realtime with strict latency requirements, embedded, systems programming. There other factors outweigh development speed.

### 7.2. Monolith vs microservices

For a 2–8 team, **start with a modular monolith**. Microservices solve independent scaling and independent multi-team work. You have neither at the start.

Signs it is time to split: more than 4–5 active developers stepping on each other in one codebase, real load requiring independent scaling, regulatory isolation requirements.

### 7.3. AGENTS.md per project

Each code repository carries its own `AGENTS.md` at the root, referencing the markdown repository's root `AGENTS.md` for shared context. Project-specific rules — typescript strict, no `any`, formatting rules, testing rules — belong in the project's local file. Shared conventions stay in the markdown repo.

### 7.4. Patterns

`patterns/` accumulates solutions to typical tasks: `data-fetching.md`, `forms.md`, `error-handling.md`, `auth.md`, `gotchas.md`. Patterns are born **from actual decisions**, not from general best practices. The first version of `forms.md` is written when you copy the same approach for the third time, not before.

### 7.5. API contracts

If you have a backend service with an HTTP API, its contract is fixed in a machine-readable form (OpenAPI for REST, GraphQL schema for GraphQL). The contract is the source of truth. Frontend types, clients for other services, and test mocks are all generated from it. Divergence between actual routes and the contract is caught in CI.

---

## 8. Design process

Figma is the UI source of truth. Figma Professional ($15–25/mo per Dev seat); the free plan does not allow useful MCP-server work (limit of 6 calls per month). Honest assessment of Claude Design as of 2026: still raw — fine for one-off presentations, early mockups, throwaway landing pages, but not for real product screens or design system work. The situation may change in 3–6 months.

The design system lives in **two places at once** — Figma (component library, variables for tokens) and code (`shared/design-system/` with components, Tailwind config or CSS variables with the same tokens) — and the two sync manually but in agreement: new component in Figma first, then in code; design changes that aren't yet in code → designer opens a PR or task; once a month, a 30-minute meeting verifying alignment. The detailed mechanics — file structure, Tokens Studio pipeline, handoff workflow — vary by team; fix them in `patterns/design-system.md` for your team. A starter recipe lives in `guides/design-process.md` if and when you need it.

Figma MCP in Claude Code lets the developer paste a Figma frame link and have Claude generate code from the frame. Setup: enable Dev Mode MCP Server in Figma; OAuth via `/mcp` in Claude Code. Limits: paid Dev seat 200 calls per day; ~25k tokens of context per response (work frame-by-frame); accuracy around 80% — spacing and components usually correct, edge cases (animations, states) often missed.

When the designer is skipped: internal admin pages, simple forms from ready components, system pages (404, 500), transactional email. When the designer is mandatory: end-user screens not built from ready components, design system changes, landing pages, critical flows. The gray zone is discussed in the weekly meeting.

---

## 9. Infrastructure and deploy

**Pay for managed services as long as you can afford it.** A self-managed Kubernetes cluster at the start is hiring one more person you don't have.

A typical starter stack for a web app: frontend hosting (Vercel, Cloudflare Pages, Netlify), backend hosting (Railway, Fly.io, Render), database (Supabase, Neon, PlanetScale), error tracking (Sentry), analytics (PostHog for product, Plausible or Fathom for web), CI/CD (GitHub Actions), secrets (Doppler, 1Password Secrets Automation, equivalent), uptime monitoring (Better Stack, UptimeRobot). Realistic cost at the early stage: $150–600/mo depending on plans and load. For a worked configuration, see `guides/infrastructure-starter-stack.md`.

Environments: `dev` (local, docker-compose) → `preview` (per-PR, isolated DB via branching) → `staging` (optional, production copy for manual QA) → `production` (deploy only from main). Preview environments are a huge win for a small team — designer and PM see the feature before merge.

PR pipeline, realistic minimum: lint and type check, unit tests, build, deploy preview, optional E2E against preview. A nine-step pipeline looks nice on paper but in a small team becomes flaky, blocks merges, and ultimately gets disabled. Add steps as you grow, when they catch real bugs.

Observability minimum: structured logs (JSON) with trace ID, error tracking, uptime monitoring, basic platform metrics. Tracing via OpenTelemetry is useful but takes time — not in the first month. `runbooks/` is written after the first incident of the corresponding kind, not before. AI agents are useful for **diagnostics** during an incident — they can walk through a runbook quickly. Only humans act in production.

Secrets, iron rules: never in git, never in chats, different for dev/staging/prod, rotation for production, access log for production secrets. Reality of a small team: rotation gets skipped. Better "every six months and actually do it" than "every 30 days and never do it".

---

## 10. Testing

There is no universal test pyramid. Your testing strategy is an ADR — discuss it, record it, revisit every six months.

Regardless of strategy, always cover: critical user journeys (5–10 in e2e — signup, payment, critical happy paths), business logic with money or security (high coverage), complex algorithms (unit tests). Don't cover: simple CRUD UI without logic, trivial getters and setters, mocks on mocks.

Honest e2e advice: start with 2–3 tests for the most important flows. Grow as you catch real bugs. Don't try to cover everything — flakiness will kill the suite.

In a small team without a QA engineer, testing is shared responsibility: developer writes tests for their feature, designer reviews the preview, PM verifies functionality before promoting to production. Hire QA when you consistently see more than one significant bug per week in production or releases delay on manual testing.

Bugs go in GitHub Issues. But their **causes and prevention** go in the repository — `journal/incidents/` for the postmortem, `patterns/gotchas.md` for "don't do this again". What broke / root cause / what we changed.

---

## 11. Rituals

The minimum that actually works.

- **Daily (async, 2 min/person):** standup in Slack or Discord — what I did, what I'm doing, blockers.
- **Weekly (sync, 45–60 min):** one whole-team meeting — retro on last week + planning for next. The retro summary lands in `journal/retros/`.
- **Monthly (sync, 1 hour):** finances and product metrics together.
- **Quarterly (sync, half a day):** strategy offsite — review root canon files, roadmap, North Star.

If you use the knowledge module, add a 20–40 minute **ingest session** every 1–2 weeks.

What to avoid: daily synchronous standups (excess for a team of four), Friday repository graph reviews (nobody looks at graph view for more than 30 seconds), separate quarterly security/compliance/data reviews (these fit into one offsite), scheduled bug bashes (postponed at week 6, deleted at week 10).

AI automations like "weekly digest from Cowork" sound nice but break after three months of neglect. The exception is the knowledge module, where ingest **is** the automation and runs in dialogue, not in the background.

---

## 12. Anti-patterns

Concrete ways teams break this framework.

### 12.1. Repository as write-only

The most common failure mode. Documents get written, but **nobody reads them**. After three months, the repository is a graveyard. Signs: ADRs never cited in PRs, new joiners do not mention the repository in their first week, discussions have "where was this written?" — "don't remember".

Antidote: repository links in every significant PR; regular "what does the repository say about this?" in discussions; onboarding through the repository, not through verbal explanations.

### 12.2. Single writer

Only one person maintains the repository (usually the founder or tech lead). When they are on vacation or leave, everything freezes.

Antidote: in the first three months every team member must write at least one page. Without this, the habit doesn't form.

### 12.3. AI generates, nobody reviews

Claude Code writes an ADR on request, nobody reviews. Six months later, half the ADRs contain hallucinated justifications.

Antidote: ADRs go through code review like any other PR. AI generation is a draft, not a final.

### 12.4. Ritual theater

Everyone formally attends meetings, writes digests, runs retros. Nothing changes from it.

Antidote: every meeting ends with a list of next actions with owners. The next meeting starts with checking that list.

### 12.5. Repository as Notion replacement

Tasks, calendars, kanban boards appear in the repository. This turns it into operational chaos.

Antidote: hard separation. Tasks — in GitHub Issues or Linear. Knowledge — in the repository. Links between them, no duplicates.

### 12.6. Reinventing GitHub

Storing `status`, `owner`, `priority`, `due_date`, and a hand-typed `## Log` section in markdown frontmatter, when GitHub Issues, Milestones, and PR timelines already track this natively. Symptom: feature pages with `status: in-progress` from three months ago.

Antidote: state lives in the issue, knowledge lives in markdown. Between them, one link: `**Tracking:** #N`.

### 12.7. Vault per tech layer

A single product is split into `backend-vault/`, `frontend-vault/`, `design-vault/`. Knowledge about a feature is scattered across layers; the team has to triangulate to answer any question.

Antidote: one repository per product. Domain-module split only when two of three triggers fire (chapter 3.3).

### 12.8. Premature module split

A team of three splits the repository into four modules "because architecturally that's correct". Creates navigation overhead where the actual workflow had none.

Antidote: split only when two of three triggers fire simultaneously. Until then, one repository, flat.

### 12.9. Expensive start

Team of three, but Kubernetes, microservices, Kafka, data warehouse. Complexity budget exhausted before the first line of business logic.

Antidote: start with a monolith on managed services. Split and complicate on actual pain.

### 12.10. ADR inflation

Forty ADRs in the first quarter, half about HTTP client choice.

Antidote: the heuristic "reversible in a day — no ADR needed".

### 12.11. Perfect repository in the first month

The team spends two weeks setting up the structure before writing code.

Antidote: start with three working directories and a few canon files. Expand on demand.

### 12.12. Knowledge without ingest

Set up the knowledge module, drop raw sources, never run ingest. The wiki is stale; the team thinks "we have a knowledge base", but in reality they don't.

Antidote: the ingest session is a ritual, not "we'll do it when there's time". See `knowledge.md` section 5.1.

### 12.13. Security/QA/analytics "later"

"PMF first, all this later". Result: the first incident or breach becomes catastrophic.

Antidote: minimum from day one — secrets in a manager, error tracking, event taxonomy, privacy policy. That is one to two weeks of work, not one day, and not three months. Details in `non-engineering.md`.

---

## 13. Phased rollout

Realistic timelines for a 2–8 team starting from scratch. **Upper-bound estimates**: reality will be slower.

### Month 1: Foundation

Goal: minimal infrastructure and the first feature through the full workflow.

- Repository, root canon files (`STACK.md`, `GLOSSARY.md`, `AGENTS.md`, `README.md`), three working directories (`decisions/`, `features/`, `patterns/`), `journal/`.
- First 2–3 ADRs: stack, baseline architecture, monorepo vs polyrepo.
- Infra: GitHub, frontend hosting, backend hosting, DB, error tracking, secrets manager.
- First feature in `features/`, taken through the full workflow (chapter 5), with a tracking GitHub Issue and a stateless spec.
- CI pipeline on PR: lint, type check, tests, preview.

What actually happens: service setup takes longer than planned (the first unexpected quirk eats a day); the first stack ADR is a draft (real understanding comes in 2–3 weeks); the first feature exposes several gaps you'll need to fill.

**Don't** connect the knowledge module in month 1, even if it seems "useful in advance". The module pays off at 10+ raw sources, which you almost certainly do not have yet.

### Months 2–3: Stabilization

Goal: rituals and patterns become habit.

- Design system with 5–10 components in code.
- `patterns/` with 3–5 written patterns.
- Weekly team ritual is stable.
- First incident with a runbook and postmortem.
- Event taxonomy in `STACK.md` or a dedicated `events.md` (see `non-engineering.md`).
- Privacy policy if needed.

Arguments of "is this in markdown or in the issue tracker" come up — resolve them case by case using Principle 2. There is a temptation to expand the repository structure — resist. Somebody starts ignoring `AGENTS.md` — discuss it.

If the team does discovery work, connect the knowledge module in months 2–3. Below 10 raw sources it is not needed.

### Months 4–6: Compounding

Goal: accumulated context starts working for the team.

- ADR base (10–20) is actually used in discussions.
- A new person onboards in 1–2 weeks.
- Claude Code in sessions behaves in the team's style without constant reminders.
- Patterns get reused in new features.
- Team velocity is higher than in months 1–2.

This is the point where the framework pays off. If at this point the team resists the repository — reconsider the approach or drop the framework. It does not fit everyone.

### After 6 months

Expansion by need: enterprise customers → components from `non-engineering.md` (compliance, security review); 10K+ users → a Data role and a separate security review; 8+ people → formal sprints, dedicated QA, DevOps; multiple modules diverging → consider a module split per chapter 3.

The repository grows organically, not by plan.

---

## Final

This framework is a scaffold, not a truth. It pays off if the team has the discipline to maintain the repository and the respect for accumulated context. Without that, it is just another folder of outdated documents.

The main thing the framework gives is an **operational language for humans working with AI agents**. Once you have set up the repository, `AGENTS.md`, patterns, and ADRs, you stop re-explaining to Claude Code how your team makes decisions every time. It reads and acts in context.

Second in importance — **surviving people leaving**. In a small team with one expert in an area, their departure is a catastrophe. With a repository where decisions are recorded, it is serious pain, but not catastrophe.

Third — **faster onboarding**. A new person becomes productive in 1–2 weeks because the team's context is readable, not retold.

Everything else is a consequence.

---

**Questions, doubts, counterexamples** — there should be plenty. The framework adapts, it doesn't copy. If after a month of use you discover that some part doesn't work for your team — that is not a mistake, that is a signal.
