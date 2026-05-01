# Shubin Framework

**Full title:** Context-First Framework for small teams with AI
**Author:** Fen
**Version:** 4.0

**Who it's for:** teams of 2–8, building a product from MVP to early traction, using AI agents (Claude Code, Cowork) as regular team members.

**What it is:** a scaffolding of practices that pays off in months 2–3 of use, given discipline.

**What it isn't:** an out-of-the-box recipe, a replacement for engineering taste, a silver bullet, a ready-made enterprise playbook.

---

## Place in the document family

This is the main document, for the team's tech lead. The other five are companions for specific needs:

- `intro.md` — short overview (200 lines), hub for all documents
- `lite.md` — for solo developers and teams of 1–2 on MVP
- `knowledge.md` — LLM wiki module following Karpathy's method, for team knowledge management
- `non-tech.md` — how to work with the vault and git without the terminal (for PM, designer, CEO)
- `non-engineering.md` — minimum on Finance, Legal, Security, Data, Support for a 2–8 team

This file covers vault architecture, the ADR process, the feature iteration cycle, development, infrastructure, rituals. For a specific sub-topic — see the corresponding companion.

---

## Before starting: three honest warnings

**1. The framework doesn't pay off immediately.** In the first month you'll spend time on setup with no visible win. In months 2–3 it starts paying off. In month 6, the team moves faster than without the framework. If you're not ready for this lag — don't start.

**2. Without discipline, nothing works.** A vault with no readers is a graveyard. ADRs without review are a formality. Rituals without consequences are theater. These aren't "set and forget" practices.

**3. Not all of this is for you.** The document describes a mature state. You start with the minimum and expand as you grow. Expand only when you actually hit the absence of a practice, not when "the framework says so".

---

## Contents

1. [Core idea](#1-core-idea)
2. [Vault: team knowledge structure](#2-vault-team-knowledge-structure)
3. [Unit of work: Feature + ADR](#3-unit-of-work-feature--adr)
4. [Feature iteration cycle](#4-feature-iteration-cycle)
5. [Roles and AI agents](#5-roles-and-ai-agents)
6. [Development](#6-development)
7. [Design process: Figma + design system in code](#7-design-process-figma--design-system-in-code)
8. [Infrastructure and deploy](#8-infrastructure-and-deploy)
9. [Testing](#9-testing)
10. [Rituals](#10-rituals)
11. [Anti-patterns](#11-anti-patterns)
12. [Phased rollout](#12-phased-rollout)

---

## 1. Core idea

### 1.1. The problem

A small team with AI agents hits two pains:

- **AI agent amnesia.** Every Claude Code session starts from scratch. Decisions made yesterday need re-explaining tomorrow.
- **Human context loss.** Three months later nobody remembers why the API returns camelCase, why this particular DB was chosen, why onboarding has 4 steps instead of 1.

Both problems are the same one: **context lives in people's heads, not in a readable form**.

### 1.2. The solution

Move team context into **a single markdown repository (the vault)**, read equally by humans and AI agents. Everything important is written there. Anything that isn't written doesn't exist for new joiners or for Claude Code.

Advantages of markdown-in-git over Notion/Confluence:

- Plaintext search and grep work
- AI agents read and edit natively
- Versioning out of the box
- No vendor lock-in
- No integrations, no API rate limits

### 1.3. The two principles behind everything

**Principle 1: Append-only for history, mutable for state.**
Decision log, feedback, incidents — only appended. Roadmap, feature pages, ADR statuses — change. This separation gives honest history without chaos.

**Principle 2: One source of truth per fact.**
Each decision, each feature, each component has exactly one canonical home. Duplicates diverge. If something is described in Linear, Figma, and the vault — within a month you'll have three divergent descriptions.

There are no other principles. Everything else is a consequence.

### 1.4. Relationship to Karpathy's method

The core of the vault (chapters 2–11) is classical engineering practice: ADRs, feature specs, patterns, runbooks. Humans write, the LLM reads.

A separate **optional module** `knowledge/` implements Andrej Karpathy's "LLM wiki" method: humans drop raw sources (interviews, articles, research), the LLM compiles them into linked wiki pages (personas, concepts, competitors). This is the only layer where the LLM is the maintainer.

The module is in a separate document: `knowledge.md`. Connect it if your team does a lot of discovery work and wants shared synthesized memory about the external world. Before that — don't.

---

## 2. Vault: team knowledge structure

### 2.1. Minimum structure to start

Don't create 12 folders on day one. Start with **three categories**:

```
vault/
├── stable/              rarely changes
│   ├── stack.md
│   ├── team.md
│   └── glossary.md
│
├── active/              main work
│   ├── features/        each feature — a folder
│   ├── decisions/       ADRs
│   └── patterns/        accumulated engineering patterns
│
├── log/                 append-only
│   ├── feedback/        folder with dated raw feedback
│   ├── incidents/       postmortems
│   └── retrospectives/
│
├── README.md
├── COMMON.md            instructions for AI agents
└── index.md
```

Optional fourth zone — `knowledge/` (LLM wiki module, see `knowledge.md`). Added when the team starts accumulating external sources (interviews, research, competitive intel) and wants to synthesize them.

Folders inside the three base zones are added when actually needed. `design/`, `specs/openapi/`, `runbooks/` appear when 2–3 files accumulate that belong there. Not earlier.

### 2.2. Why `stable/active/log` and not `domain/product/engineering/feedback`

This is a deliberate choice of a change-mode classification (by nature of changes) over a domain classification (by type of content). Rationale:

- The change-mode scheme directly reflects Principle 1 ("append-only for history, mutable for state"). The structure hints at behavior.
- Fewer categories — three instead of five or six. Fewer "where does this go" moments.
- The domain scheme (`product/`, `engineering/`, `design/`) associates with org roles and provokes silos: "that's from engineering, I don't touch it".

**Alternative.** If your team thinks in domain terms, or comes from an established Notion space with that structure — change-mode names may feel abstract. In that case, using `foundation/`, `domain/`, `product/`, `engineering/`, `feedback/` is acceptable. The main thing — **pick one approach and don't mix**. Changing structure 6 months in is a painful migration.

For new teams I recommend `stable/active/log` by default. For teams migrating from Notion — discuss it explicitly in the first ADR.

### 2.3. What lives in each zone

**stable/** (rarely changes):

- `stack.md` — tech stack
- `team.md` — who owns what
- `glossary.md` — domain term glossary
- `north-star.md` — main metric (when it appears)
- `events.md` — event taxonomy (when it appears, see the non-engineering companion)

**active/** (active work):

- `features/` — one folder per feature, see chapter 3
- `decisions/` — ADRs, sequentially numbered
- `patterns/` — engineering code patterns, gotchas

**log/** (append-only, never edited):

- `feedback/` — folder with dated note files: `2026-04-18-user-X-interview.md`
- `incidents/` — postmortems
- `retrospectives/` — weekly/quarterly retro summaries

Domain knowledge about the external world (personas, competitors, concepts) does not live in stable/. It either accumulates briefly in `stable/glossary.md`, or — if the team uses the knowledge module — in `knowledge/wiki/`.

### 2.4. Why not Notion

Fair question: what's different from a well-organized Notion workspace? Four things:

1. **AI agents read and write natively** — no API, no tokens, via regular file operations.
2. **Git versioning** — you can see who, when, what changed. `git log` + `git blame` give decision archeology.
3. **Grep and ripgrep** — text search works instantly, including from CLI for agents.
4. **No vendor lock-in** — moving to another platform = `git clone`.

Notion MCP exists, but markdown-in-git is faster, cheaper, and more reliable for a team with Claude Code on every machine.

### 2.5. COMMON.md as a schema

A single file at the vault root describing rules for AI agents working with the vault. Injected via symlink or import into every project where Claude Code runs.

Minimal example:

```markdown
# Vault Schema

## Structure

- stable/ — rarely changes, source of truth
- active/ — active work on features and decisions
- log/ — append-only history
- knowledge/ — optional LLM wiki module (see knowledge/CLAUDE.md)

## When writing code

1. Read active/features/<current-feature>/index.md
2. Check active/decisions/ for relevant ADRs
3. Check active/patterns/ for similar patterns
4. On a new significant decision — propose creating an ADR

## On a significant finding

If you discover a gotcha, pattern, or decision worth preserving —
ask the user whether to record it in active/patterns/.
Don't create files silently.

## What not to do

- Don't edit log/ (append-only)
- Don't create empty folders "for the future"
- Don't work with knowledge/ under general rules —
  it has its own CLAUDE.md with different maintainer mechanics
```

COMMON.md grows as the team learns. Honestly: in the first month it's short and updated often. By month 3 it stabilizes.

### 2.6. Vault as a separate repository

One vault repository, independent of code. Other repositories attach it in one of these ways:

- git submodule (harder, but versions are pinned)
- git clone as a peer (easier, but everyone holds local copies)
- symlink after clone (for CLAUDE.md → COMMON.md)

The choice goes in the team's first ADR. Mixing methods within one team is bad — pick one and stick with it.

---

## 3. Unit of work: Feature + ADR

### 3.1. Feature as the canonical page

Each feature lives in `active/features/<slug>/index.md`:

```markdown
---
status: in-progress       # idea | validated | in-progress | shipped | killed
owner: @alice
priority: P1              # P0 | P1 | P2 | P3
created: 2026-04-17
target: 2026-05-01
---

# User Auth

## Why

One sentence on why this is needed and for whom.

## Scope

What exactly we're building. Boundaries: what we're NOT doing.

## Success criteria

Measurable check that the task is done.

## Technical

Links to [[decisions/0015-auth-strategy]]

## Design

Link to a Figma frame.

## Implementation

PR: #123

## Log

- 2026-04-17: created
- 2026-04-20: backend ready
- 2026-04-22: shipped
```

This is the unit of work, not a ticket in Linear. The ticket links here, not the other way around.

### 3.2. ADR

Architectural Decision Record — a document about a decision that:

- Is hard to reverse
- Affects several parts of the system
- Will need explaining three months from now

A good boundary heuristic: **if the decision can be reversed in a day of one person's work — no ADR needed**. If the reversal is a week or more — ADR needed.

Format:

```markdown
# ADR-0015: <short decision title>

## Status

Accepted — 2026-04-17

## Context

What's the situation, what are the constraints.

## Options considered

Options with pros/cons. Even rejected ones are useful:
six months later you'll remember why you didn't go that way.

## Decision

Chosen option + a sentence or two on why.

## Consequences

What we lose, what we gain, what becomes harder.

## Related

Links to features and services.
```

Numbering is sequential and never reset. Outdated ones get status `Deprecated` or `Superseded by ADR-NNNN`.

If the team uses the knowledge module, an ADR can optionally include a **Research context** section with links to `knowledge/wiki/personas/` and `knowledge/wiki/competitors/` — this gives traceability to actual interviews and research.

### 3.3. ADR-first — the ideal we aim for

Ideally a significant decision starts with a PR changing only the ADR. Discussion, approval, merge. Then code.

In practice, in a small team the tech lead often decides in their head and writes the ADR after some code is already written. That's fine. What matters is that the ADR eventually exists, even if written after the fact.

Danger: ADRs for ADRs' sake. If you have 40 ADRs in the first quarter — half of them are likely about HTTP client choice, and you've turned the practice into bikeshedding. A working norm is 5–15 ADRs in the first 6 months of active development.

---

## 4. Feature iteration cycle

This is the central chapter of the framework. Here the vault, Figma, Claude Code, and git converge into a single workflow. Full cycle from idea to production with every artifact.

### 4.1. Cycle overview

```
       ┌─────────────────────────────────┐
       │  1. Idea in the vault           │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  2. Spec (Why + Scope)          │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  3. ADR (if needed)             │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  4. Design in Figma             │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  5. Implementation (Claude Code)│
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  6. Preview + review            │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  7. Merge + deploy production   │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  8. Close in the vault          │
       └────────────┬────────────────────┘
                    ▼
       ┌─────────────────────────────────┐
       │  9. Feedback loop               │
       └─────────────────────────────────┘
```

Each step produces concrete artifacts in concrete places.

> **Note on the running example.** This chapter uses a fictional "onboarding" feature with specific numbers (60% drop-off, 145s time, etc.). All numbers are illustrative, not based on real data. The goal is to show artifact formats and transitions between steps, not to provide benchmarks.

### 4.2. Step 1: Idea

**Who:** usually PM or founder. Could be anyone on the team.
**Where:** a new folder `active/features/<slug>/` in the vault, file `index.md`.
**Artifact:** a draft with YAML frontmatter and a one-sentence "why".

```markdown
---
status: idea
owner: @alice
priority: P2
created: 2026-04-18
---

# Onboarding redesign

## Why

Current onboarding — 60% drop-off on the second screen. Hypothesis:
too many fields at once.

## Scope

TBD after discussion.
```

**In git:** create a branch `feature/onboarding-redesign` in the vault repo, commit the draft, open a PR.
**Trigger for next step:** discussion in the weekly meeting, decision to build it.

### 4.3. Step 2: Spec

**Who:** PM + one of the developers (for technical realism) + a designer (for UX).
**Where:** same `index.md`, extended with sections.
**Artifact:** filled-in Scope and Success criteria sections.

```markdown
## Scope

- 4 screens instead of 5
- Progress bar on top
- Option to skip onboarding
- Progress saving (if user leaves — resume)

NOT doing in this iteration:

- Video tutorial
- Per-plan personalization
- A/B test of variants

## Success criteria

- Drop-off on screen 2 < 30% (now 60%)
- Median completion time < 90 sec
- Measurement 2 weeks after release
```

**In git:** update the same PR in the vault, status changes to `validated`.
**Trigger for next step:** approve the PR, merge into the vault's main.

### 4.4. Step 3: ADR (if needed)

**When needed:** if the spec contains a non-trivial technical decision affecting more than one part of the system. In our example: "progress saving" — where to store it? localStorage? DB? That's a decision → ADR.

**Who:** the developer who'll implement it.
**Where:** `active/decisions/00NN-onboarding-progress-storage.md`.

```markdown
# ADR-0023: Storing onboarding progress

## Status

Accepted — 2026-04-19

## Context

User can leave onboarding and return later. We need to save
which step they're on, what they've already filled in.

## Options considered

### 1. localStorage

- Simple, zero server logic
  − Lost on device change

### 2. Field in users table

- Cross-device
  − Migration

### 3. Separate onboarding_progress table

- Clean separation
  − Extra table for one feature

## Decision

`onboarding_state JSONB` field in users table.

## Consequences

- Cross-device
  − Migration (see PR in the backend repo)
```

**In git:** PR in the vault, same or separate, as convenient.

### 4.5. Step 4: Design

**Who:** designer. If there's no designer — a developer with ready-made components.
**Where:** the product's Figma project, file `[Feature] Onboarding`. Details in chapter 7.

**Artifact:**

- Frames of 4 screens in section `In progress`
- Transition prototype
- Use of existing components from the Design System

**In the vault:** in `active/features/onboarding/index.md` the Design section is extended with a link to the Figma page:

```markdown
## Design

Figma: https://figma.com/file/.../Onboarding

- Section "In progress" — final screens
- Prototype: Play mode for demo transitions
```

### 4.6. Step 5: Implementation

**Who:** developer + Claude Code.

**Preparation:**

- `git pull` in the vault repo — get latest changes
- `git checkout -b feature/onboarding` in the code repo

**Claude Code session:**

```
cd ~/projects/my-app
claude

> Implement the onboarding feature. Context:
> - active/features/onboarding/index.md
> - active/decisions/0023-onboarding-progress-storage.md
> - Figma: [link from index.md]
>
> Use Figma MCP to read frames.
> Use components from shared/design-system.
> Check active/patterns/forms.md.
```

Claude Code reads the vault pages and the ADR, uses Figma MCP to read frames, generates code. The developer reviews, fixes edge cases (Claude often misses loading states and error handling), writes tests for critical logic.

**Artifact:** PR in the code repo with the implementation.

**In the vault:** the developer adds the PR link to the Implementation section:

```markdown
## Implementation

- Frontend PR: my-app#123
- Backend PR: api#45 (migration + endpoint)
```

### 4.7. Step 6: Preview + review

Automatic: on each push to a feature branch — a preview environment is deployed.

The designer opens the preview, compares with Figma. Discrepancies — as PR comments. PM verifies the Scope is met. Developer fixes, pushes. The cycle repeats, usually 1–3 iterations.

### 4.8. Step 7: Merge + deploy production

Approve from 1–2 people (depends on team size; in a team of 2 — from the other person, in a team of 4+ — from two). Merge to main → automatic deploy to production.

At this moment the feature is live for users.

### 4.9. Step 8: Close in the vault

**Who:** usually whoever did the merge.
**Where:** `active/features/onboarding/index.md`.

```markdown
---
status: shipped     ← was: in-progress
shipped: 2026-04-28
---

...

## Log

- 2026-04-18: created
- 2026-04-19: ADR-0023 accepted
- 2026-04-22: design ready
- 2026-04-28: shipped, deploy v1.4.2
```

**In git:** a small PR in the vault with only the status update.

### 4.10. Step 9: Feedback loop

Two weeks after release (per the success criteria from step 2), the PM or designer:

1. Checks metrics in PostHog.
2. Creates `log/feedback/2026-05-12-onboarding-results.md` with numbers, user quotes, conclusions.
3. If changes are needed — a new feature in `active/features/`.

If the team uses the knowledge module — significant insights from this feedback file flow into the next wiki ingest cycle.

### 4.11. What happens in each repo

For one medium-sized feature cycle, the vault repo gets 5–8 commits:

1. `feat: add onboarding redesign idea`
2. `spec: scope and success criteria for onboarding`
3. `adr: 0023 onboarding progress storage`
4. `design: add figma link for onboarding`
5. `impl: add PR links for onboarding`
6. `status: onboarding shipped`
7. `feedback: onboarding results`

In the frontend repo: branch `feature/onboarding`, PR with implementation. In the backend repo: branch `feature/onboarding-progress`, PR with migration and endpoint.

Commits in the vault are small and informative. That's the norm, not "too many commits".

### 4.12. What to do when reality diverges from plan

Typical situation: at step 5 the developer realizes the approach chosen in the ADR doesn't work.

**Don't:** silently change the code, forgetting the ADR.

**Do:**

1. Update ADR-0023: status → `Superseded by ADR-0024`.
2. Create ADR-0024 with the new decision and reason for rejecting the old one.
3. Update `active/features/onboarding/index.md` if scope changed.

It takes 15 minutes. Six months later nobody scratches their head about why the code says one thing and the ADR says another.

---

## 5. Roles and AI agents

### 5.1. Base roles

In a 2–8 team, roles are functions, not titles. One person can carry 2–3 roles; what matters is each function being explicit.

| Function    | What it does                 | Owns in the vault                                             |
| ----------- | ---------------------------- | ------------------------------------------------------------- |
| Product     | Holds "why we're doing this" | active/features/\* (Why section), log/feedback/               |
| Design      | Visual and UX logic          | active/features/\*/design                                     |
| Engineering | Code and architecture        | active/patterns, active/decisions, features/\*/implementation |
| Ops         | Infra, deploy, incidents     | runbooks/, infra/                                             |

Specialized roles (DevOps, QA, Security, Data) — added on actual need, not by plan. The "it's time" signal is when the existing team steadily spends over 20% of its time in that zone.

### 5.2. AI agents as part of the team

Key idea: each AI agent has a **configuration** that makes it a disciplined team member, not a general-purpose chatbot.

- **Claude Code in every code repo** — its own CLAUDE.md referencing vault's COMMON.md
- **Cowork** — configured once on the machine of the person using it
- **Claude Design** — optional and with care: only for one-off prototypes and presentations (see 7.1)

This isn't "six virtual job titles in a table". It's **one or two active agents per person per day**, configured to read the vault and follow its conventions.

### 5.3. AI delegation boundaries

AI agents do:

- Generate code from a specification
- Read the vault and answer in context
- Write ADR drafts on request
- Help with routine changes
- Maintain the knowledge wiki (when the module is used) — compile raw into wiki

AI agents do NOT:

- Don't make product decisions
- Don't act in production without human-in-the-loop
- Don't write ADRs without human review (otherwise you get hallucinated trade-offs)
- Don't commit to main without a PR and review

---

## 6. Development

### 6.1. Stack choice

The framework **doesn't dictate a stack**. The choice depends on team experience, product requirements, hiring in your region, runway budget.

The main rule: **write the team's first ADR about the stack**. Whatever the choice, it must be argued and recorded.

Key questions for the stack ADR:

- Who on the team already knows the technology productively?
- Is it realistic to hire another developer in this stack in your region?
- What library and managed-services ecosystem is around it?
- What are the trade-offs in compile time, error messages, learning curve?

**Pragmatic default for web CRUD:** TypeScript/Node.js. Reasons: one language across the stack, mature ecosystem, easier hiring. A boring choice — and usually the right one.

**Exceptions:** heavy computation (ML serving, video/audio processing), realtime with strict latency requirements, embedded, systems programming — there other factors outweigh development speed.

### 6.2. Monolith vs microservices

For a 2–8 team, **start with a modular monolith**. Microservices solve the problem of independently scaling parts of the system and independent work of multiple teams on one system. You have neither at the start.

Signs it's time to split:

- More than 4–5 active developers stepping on each other in one codebase
- Real load requiring independent scaling of parts
- Regulatory requirements requiring isolation

Until then — a modular monolith with clear internal module boundaries.

### 6.3. CLAUDE.md per project

The key config file at the root of each code repository. Contents:

```markdown
# <Project name>

## Context

Common context: ../AGENTS.md
Current features: ../features/

## Stack

See ../STACK.md

## Before writing code

1. Find the feature in ../features/
2. Check ../decisions/ for relevant ADRs
3. Check ../patterns/ for similar patterns
4. Check ../patterns/gotchas.md

## Rules

- (project-specific rules: typescript strict, no any, etc)
- (formatting, linting rules)
- (testing rules)

## Deploy

See ../vault/infra/deployment.md (if present)
```

### 6.4. Patterns in the vault

As development progresses, `active/patterns/` accumulates solutions to typical tasks. Minimum for a web app:

- `data-fetching.md` — how you talk to the server
- `forms.md` — validation and error display
- `error-handling.md` — error strategy
- `auth.md` — how authorization is checked
- `gotchas.md` — what burned you (most valuable over time)

Patterns are born **from actual decisions**, not from general best practices. The first version of `forms.md` is written when you copy the same form approach for the third time.

### 6.5. API contracts

If you have a backend service with an HTTP API, its contract is fixed in a machine-readable form (OpenAPI for REST, GraphQL schema for GraphQL).

The contract is the source of truth. Types for the frontend, clients for other services, test mocks — all generated from it. Divergence between actual routes and the contract is caught in CI.

---

## 7. Design process: Figma + design system in code

### 7.1. Real state of Claude Design (April 2026)

Honest assessment after a few weeks in production: **Claude Design is raw**. These are qualitative observations from early users — your experience on a specific product may differ. Typical issues: generates "average" UI without character, distorts the design system in non-trivial cases, handoff to code requires rewriting a significant portion.

**Where Claude Design is OK:** one-off presentations, early mockups in discussions, throwaway landing pages.

**Where NOT to use in production:** real product screens, design system work, design that matters to UX.

The situation may change in 3–6 months. For now Figma remains the only serious design tool.

### 7.2. Figma as the UI source of truth

**Figma Professional** ($15–25/mo per Dev seat). The free plan doesn't allow normal MCP-server work (limit of 6 calls per month).

**What's in Figma:** design system with components and tokens, final mockups of non-trivial screens, prototyping for user tests.

**What's NOT in Figma:** simple UI (3-field forms), "why" decisions (those are in the vault), design-system components as the first source (those are in code, see 7.3).

### 7.3. Figma file structure and the design system

One Figma project per product. Inside:

```
📁 Product Name
├── 📄 Design System         ← components, tokens
├── 📄 [Feature] User Auth   ← one page per major feature
├── 📄 [Feature] Billing
└── 📄 Archive               ← old iterations
```

Inside each feature file — sections `Current`, `In progress`, `Explorations`. Mixing them in one frame = the main source of "the designer made one thing, the developer made another".

The design system lives **in two places at once**, and they sync manually but in agreement.

**In Figma:** component library with variants, variables for tokens.
**In code:** `shared/design-system/` with React components, Tailwind config or CSS variables with the same tokens.

**Sync rules:**

- New component in Figma first, then in code.
- Change in Figma without code change → designer opens a PR or creates a task.
- Once a month — a 30-minute sync meeting: "does everything still match".

Detailed Figma file organization, Tokens Studio pipeline, handoff workflow — if you actively need this, fix it in `active/patterns/design-system.md` for your team. A generic recipe here is useless, it varies widely.

### 7.4. Figma MCP in Claude Code

The integration that lets Claude Code read Figma files directly.

**Setup:**

1. In Figma: Settings → Enable Dev Mode MCP Server.
2. In Claude Code: `/mcp` → figma → OAuth.

**Use:** designer selects a frame → Copy link to selection. Developer pastes into Claude Code: "Implement this screen: [link]". Claude reads the frame via MCP and generates code.

**Limits:**

- Free Figma: 6 calls per month (not enough).
- Paid Dev seat: 200 calls per day.
- Context per response: 25k tokens — work frame-by-frame, not whole pages.
- Accuracy ~80%: spacing and components usually correct, edge cases (animations, states) often missed.

### 7.5. When the designer is skipped

Not every UI element needs a designer. Pragmatic for a 2–8 team:

**No designer, developer does it themselves:** internal admin pages, simple forms from ready-made components, system pages (404, 500), transactional email.

**Designer mandatory:** any end-user screen not built from ready components; changes to the design system; landing pages; critical flows.

The gray zone — discussed in the weekly meeting.

---

## 8. Infrastructure and deploy

### 8.1. Philosophy

**Pay for managed services as long as you can afford it.** A self-managed Kubernetes cluster at the start is hiring one more person you don't have.

### 8.2. Starter stack

Typical set for a web app:

- **Frontend hosting** — Vercel, Cloudflare Pages, or Netlify
- **Backend hosting** — Railway, Fly.io, or Render
- **Database** — Supabase, Neon, PlanetScale
- **Error tracking** — Sentry
- **Analytics** — PostHog (product), Plausible/Fathom (web)
- **CI/CD** — GitHub Actions
- **Secrets** — Doppler, 1Password Secrets Automation, or equivalent
- **Monitoring** — Better Stack, Axiom, or built-in platform features

Realistic cost at the early stage: $150–600/mo depending on chosen plans and load. Services scale differently (managed DBs tend to jump sharply between tiers, frontend hosting grows more gradually).

### 8.3. When to think about Kubernetes

Practically never, until you have:

- A dedicated DevOps engineer
- 5+ services with different requirements
- Load that managed platforms can't handle
- A self-hosted requirement from compliance

If yes to all of the above — managed Kubernetes (GKE, EKS, AKS), not self-hosted.

### 8.4. Environments

- **dev** — local, docker-compose
- **preview** — per PR, isolated DB via branching
- **staging** — optional, production copy for manual QA
- **production** — deploy only from main

Preview environments are a huge win for a small team. Designer and PM see the feature before merge.

### 8.5. PR pipeline

Realistic minimum:

1. Lint + type check
2. Unit tests
3. Build
4. Deploy preview
5. (Optional) E2E against preview

A full pipeline of 9+ steps looks nice on paper, but in a small team often becomes flaky, blocks merges, and ultimately gets disabled. Add steps as you grow, when they actually catch real bugs.

### 8.6. Observability

Minimum:

- Structured logs (JSON) with trace ID
- Error tracking (Sentry)
- Uptime monitoring (Better Stack, UptimeRobot)
- Basic metrics from the hosting platform

Tracing via OpenTelemetry is useful but takes time to set up. Not in the first month.

### 8.7. Runbooks

`infra/runbooks/` — pages of "what to do when X breaks". Written after the first incident of the corresponding kind, not before.

AI agents are useful for **diagnostics** during an incident — they can walk through a runbook quickly, check logs, propose hypotheses. But **only humans act in production**. Autonomous AI incident response is not 2026.

### 8.8. Secrets

Iron rules: never in git, never in chats, different for dev/staging/prod, rotation for production (frequency you decide), access log for production secrets.

Reality of a small team: rotation gets skipped often. Better "every 6 months and actually do it" than "every 30 days and never do it".

---

## 9. Testing

### 9.1. Strategy

There's no single right test pyramid for all teams. The choice depends on system type:

- UI-heavy with simple logic — unit tests on utilities and hooks, e2e on critical flows
- API-heavy with complex logic — lots of integration, less unit
- Microservices — contract tests between services

Your strategy is an ADR. Discuss, record, revisit every six months.

### 9.2. What to always cover

Regardless of strategy:

- **Critical user journeys** — 5–10 in e2e (signup, payment, critical happy path)
- **Business logic with money or security** — 80–90%+ coverage
- **Complex algorithms** — unit tests

Don't cover: simple CRUD UI without logic, trivial getters/setters, mocks on mocks.

### 9.3. E2E: reality vs ideal

Playwright and equivalents are powerful, but flaky tests take time to stabilize. Visual regression often produces false positives.

Honest recommendation: start with 2–3 e2e for the most important flows. Grow as you catch real bugs. Don't cover everything.

### 9.4. QA without a QA engineer

In a small team, testing is shared responsibility. The developer writes tests for their feature. The designer reviews the preview environment. The PM verifies functionality before promoting to production.

When to hire a dedicated QA: consistently >1 significant bug per week in production, releases delayed by manual testing.

### 9.5. Bug tracking

Bugs go in Linear or GitHub Issues. But **their causes and prevention** go in the vault. A significant bug → a short page in `log/incidents/` or a line in `active/patterns/gotchas.md` with the structure: what broke / root cause / what we changed in the pattern.

---

## 10. Rituals

### 10.1. The minimum that actually works

The list is deliberately short. More rituals = more time in synchronous meetings = less real work.

**Daily (async, 2 minutes per person):** standup in Slack/Discord — what I did, what I'm doing, blockers.

**Weekly (sync, 45–60 minutes):** one whole-team meeting — retrospective on last week + planning for next. Includes the sprint ritual, if you use sprints.

**Monthly (sync, 1 hour):** finances + product metrics together — where we are vs. a month ago.

**Quarterly (sync, half a day):** strategy offsite — review `stable/`, roadmap, North Star.

If you use the knowledge module, add an **ingest session** every 1–2 weeks (20–40 minutes): a developer runs Claude Code on new raw sources.

### 10.2. What to avoid

Rituals that sound nice and die after a month:

- Daily synchronous standup (for a team of 4 it's excess)
- Friday vault review in graph view (nobody looks at the graph view for more than 30 seconds)
- Separate quarterly security/compliance/data reviews (in a small team this fits into one offsite)
- Scheduled bug bash (after 6 weeks it gets postponed, after 10 it gets deleted)

### 10.3. AI automations: honestly

In theory Cowork could read the vault on Mondays, produce a digest, flag anomalies. In practice: this needs writing and debugging, API integrations need maintenance, after 3 months of neglect such automation often breaks.

If you have the human resources to build and maintain them — great, these automations pay off. If not — do it by hand. Don't put "automatic weekly digest" into the rollout plan as a given.

Exception — the knowledge module, where ingest through the LLM **is** the main automation, and it runs in dialogue mode, not in the background. This is the only automation I recommend.

---

## 11. Anti-patterns

Concrete ways teams break this framework.

### 11.1. Vault as write-only

The most common failure mode. Documents get written, but **nobody reads them**. After 3 months, the vault is a graveyard.

Signs: ADRs never cited in PRs, new team members don't mention the vault in their first week, discussions have "where was this written?" — "don't remember".

Antidote: vault links in every significant PR, regular "what does the vault say about this?" in discussions, onboarding through the vault — not through verbal explanations.

### 11.2. Single writer

Only one person maintains the vault (usually the founder or tech lead). When they're on vacation or leave — everything freezes.

Antidote: in the first 3 months every team member must write at least one vault page. Without this, the habit doesn't form.

### 11.3. AI generates, nobody reviews

Claude Code writes an ADR on request, nobody reviews. Six months later, half the ADRs contain hallucinated justifications.

Antidote: ADRs go through code review like any other PR. AI generation is a draft, not a final. For the knowledge module the rule is stricter — see the corresponding document.

### 11.4. Ritual theater

Everyone formally attends meetings, writes digests, runs retros. But nothing changes from it.

Antidote: every meeting ends with a list of next actions with owners. The next meeting starts with checking that list.

### 11.5. Vault as a Notion replacement

Tasks, calendars, kanban boards appear in the vault. This turns it into operational chaos.

Antidote: hard separation. Tasks — in Linear/GitHub Issues. Knowledge — in the vault. Links between them, but not duplicates.

### 11.6. Expensive start

Team of 3, but Kubernetes, microservices, Kafka, data warehouse. Complexity budget exhausted before the first line of business logic.

Antidote: start with a monolith on managed services. Split and complicate on actual pain.

### 11.7. ADR inflation

40 ADRs in the first quarter, half about HTTP client choice.

Antidote: the heuristic "reversible in a day — no ADR needed".

### 11.8. Perfect vault in the first month

The team spends 2 weeks setting up the vault structure before writing code.

Antidote: start with 3 folders. Expand on demand.

### 11.9. Knowledge without ingest

You set up the knowledge module, you drop raw sources, but you don't run ingest. The wiki is stale, the team thinks "we have a knowledge base", but in reality they don't.

Antidote: the ingest session is a ritual, not "we'll do it when there's time". See `knowledge.md` section 5.1.

### 11.10. Security/QA/analytics "later"

"PMF first, all this later". Result: the first incident or breach becomes catastrophic.

Antidote: minimum from day one — secrets in a manager, error tracking, event taxonomy, privacy policy. That's 1–2 weeks of work, not 1 day (as often promised), and not 3 months. Details — in `non-engineering.md`.

---

## 12. Phased rollout

Realistic timelines for a 2–8 team starting from scratch. These are **upper-bound estimates**: reality will be slower.

### Month 1: Foundation

Goal: minimal infrastructure and the first feature through the full workflow.

- Vault repository, 3 folders (stable, active, log), README, COMMON.md
- First 2–3 ADRs: stack, baseline architecture, monorepo vs polyrepo
- Infra: GitHub, frontend hosting, backend hosting, DB, error tracking, secrets manager
- First feature in `active/features/`, taken through the full workflow (chapter 4)
- CI pipeline on PR: lint, type check, tests, preview

What actually happens:

- Service setup takes longer than planned (the first unexpected quirk will eat a day).
- The first stack ADR will be a draft — real understanding comes in 2–3 weeks.
- The first feature will expose several gaps in the vault you'll need to fill.

**What you DO NOT do in month 1:** don't connect the knowledge module, even if it seems "useful in advance". The module pays off at 10+ raw sources, which you almost certainly don't have in month 1.

### Months 2–3: Stabilization

Goal: rituals and patterns become habit.

- Design system with 5–10 components in code
- `active/patterns/` with 3–5 written patterns
- Weekly team ritual is stable
- First incident with a runbook and postmortem
- Event taxonomy in `stable/events.md`
- Privacy policy, if needed (see non-engineering)

What actually happens:

- Arguments of "is this in the vault or in Linear" will come up — resolve them case by case.
- There will be a temptation to expand the vault structure — resist.
- Somebody on the team will start ignoring COMMON.md — discuss it.

If the team does discovery work (interviews, research) — in months 2–3 connect the knowledge module. Below 10 raw sources it's not needed, but at 10+ the module gives a meaningful effect.

### Month 4–6: Compounding

Goal: accumulated context starts working for the team.

- ADR base (10–20) is actually used in discussions
- A new person onboards in 1–2 weeks
- Claude Code in sessions behaves in the team's style without constant reminders
- Patterns get reused in new features
- Team velocity is higher than in months 1–2

This is the point where the framework pays off. If at this point the team resists the vault — reconsider the approach or drop the framework. Doesn't fit everyone.

### After 6 months

Expansion by need:

- Enterprise customers appear → components from non-engineering (compliance, security review)
- 10K+ users → a Data role appears and a separate security review
- 8+ people → formal sprints, dedicated QA, DevOps
- Multiple services stepping on each other → split into microservices

The vault grows organically, not by plan.

---

## Final

This framework is a scaffold, not a truth. It pays off if the team has the discipline to maintain the vault and the respect for accumulated context. Without that, it's just another folder of outdated documents.

The main thing the framework gives is an **operational language for humans working with AI agents**. Once you've set up the vault, CLAUDE.md, patterns, and ADRs, you stop re-explaining to Claude Code how your team makes decisions every time. It reads and acts in context.

Second in importance — **surviving people leaving**. In a small team with one expert in an area, their departure is a catastrophe. With a vault where decisions are recorded — it's serious pain, but not catastrophe.

Third — **faster onboarding**. A new person becomes productive in 1–2 weeks because the team's context is readable, not retold through verbal conversations.

Everything else is a consequence.

---

**Questions, doubts, counterexamples** — there should be plenty. The framework adapts, it doesn't copy. If after a month of use you discovered that some part doesn't work for your team — that's not a mistake, that's a signal.
