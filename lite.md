# Shubin Framework: lite version

**Full title:** Repository for small projects — lite version
**Part of:** Shubin Framework (starter document)

**Who it's for:** 1–2 people working on an MVP or pet project with Claude Code. Speed matters more than process. No plans to hire yet.

**What it is:** minimal practice of using a markdown folder as external memory for Claude. No ADRs, no rituals, no three-tier `AGENTS.md` hierarchy. Just enough so Claude stops forgetting context between sessions. Plus — an optional knowledge module (Karpathy's method) for ingesting external sources.

**Setup time:** 15 minutes basic, +10 minutes for the knowledge module (optional).

**Related documents (when the project grows):**
- `framework.md` — full framework for teams of 2–8 with roles, ADRs, rituals
- `knowledge.md` — full version of the knowledge module (a simplified section is included here)
- `non-tech.md` — guide for non-technical collaborators

---

## Why bother

You work with Claude Code. On Monday you agreed to use Supabase, not Firebase. On Friday, new session — Claude suggests Firebase again. You explain. Next Tuesday — again. It's annoying and eats up an hour per week in repeated explanations.

A small `notes/` folder solves exactly this. Written once — Claude reads it every session.

You don't need to solve other problems at this stage. ADRs, roles, rituals, event taxonomy — those are for teams that need coordination. Your coordination is in one place: your head.

**Bonus for pet projects and MVPs:** the optional knowledge module (see below, "When you need external sources") — automatic compilation of interviews / articles / research into linked pages. For solo developers this saves hours of manual synthesis.

---

## Minimal structure

One folder next to the project:

```
my-project/
├── .claude/         ← the project itself
├── src/
├── package.json
└── notes/           ← this
    ├── decisions.md
    └── todo.md
```

Two files to start. That's it. Not four, not five.

### What goes in each file

**`decisions.md`** — decisions you made, with reasons. One line per decision:

```markdown
# Decisions

## 2026-04-10
- Supabase, not Firebase (need Postgres + RLS)
- shadcn/ui for components
- Zustand for state (Redux is overkill)
- Deploy on Vercel

## 2026-04-15
- Pricing via Stripe Checkout, not custom checkout
- Email via Resend

## 2026-04-20
- Dropped real-time via WebSocket → SSE is enough
- Image uploads on Cloudflare R2, not S3
```

Write when you make a decision. Not an "architecture document" — just a dated list.

**`todo.md`** — what to do next. Not a task manager, just a list:

```markdown
# TODO

## Now
- [ ] Docs page with FAQ
- [ ] Email notifications for comments
- [ ] Rate limiting on API

## Later
- [ ] Admin dashboard
- [ ] Data export
- [ ] Webhook on new subscription

## Ideas
- AI onboarding helper
- Public profiles
```

### Two other files — on demand, not upfront

These files are added **when you have something to put in them**, not on day one. Principle: don't create folders and files in advance.

**`patterns.md`** — you create it the third time you copy a similar chunk of code. Common patterns in your codebase:

```markdown
# Patterns

## Forms
react-hook-form + zod. Example: components/SignupForm.tsx

## Client-side error handling
All fetches go through lib/api.ts. Errors shown via toast from sonner.

## Loading states
Skeleton components in components/skeletons/.
We don't use spinners — they jitter.

## Auth
Supabase Auth. Check in middleware.ts, not in every route.
```

**`gotchas.md`** — you create it when you get burned by your first non-obvious bug. Write while fresh, so you don't forget:

```markdown
# Gotchas

## Next.js App Router + Supabase SSR
Need createServerClient, not createClient.
Otherwise cookies don't get passed, user is "not logged in" on the server.

## Tailwind in server components
Dynamic classes via clsx/cn don't work — Tailwind
doesn't see them at build time. Always use static strings.

## Resend in dev
Domain must be verified even for test emails to your own address.
Otherwise 403.
```

By the end of the first month, you'll usually have all four files. But don't spend 5 minutes creating empty files on day 1 — you'll fill them naturally during the first week.

---

## How Claude Code uses this

Create an `AGENTS.md` file in the project root:

```markdown
# Project notes

Before working, read:
- notes/decisions.md — architectural decisions
- notes/todo.md — priorities
- notes/patterns.md (if exists) — common code patterns
- notes/gotchas.md (if exists) — known pitfalls

When we make a significant decision or discover a gotcha —
suggest adding it to the corresponding file. Don't create files silently.
```

That's it. `AGENTS.md` is the cross-tool standard for AI-agent instructions; Claude Code reads it (along with `CLAUDE.md`) automatically when started in this folder, picks up links to `notes/`, and works in context.

---

## Workflow

### When to write in notes

**In `decisions.md`** — when you made a decision you'd find annoying to explain again a month later. Usually about:
- Choice of library or service
- Architectural direction (auth, deployment, state)
- Rejection of some approach ("we tried X, didn't fit")

Don't write about variable names and formatting — that's not decisions, that's style.

**In `patterns.md`** — when you catch yourself doing something similar for the third time. First time — just code. Second — "this might be a pattern". Third — definitely a pattern, write it down.

**In `gotchas.md`** — immediately after figuring out a strange bug. While it's fresh. A week later, you'll forget the details.

**In `todo.md`** — freely. It's your priorities draft, not a commitment.

### How often to update

No schedule. Update when you notice something worth writing down. If you didn't add a line for a week — no problem. If you added five in a day — also fine.

**Important:** write concisely. Two lines instead of a paragraph. The goal is for you and Claude to orient quickly, not "beautifully documented".

### How to delete outdated stuff

Don't. Just replace with the new decision. History doesn't matter at this stage.

If `decisions.md` grew past 200 lines — that's a signal your project isn't small anymore, time to look at the full version (`framework.md`).

---

## When you need external sources: the knowledge module

If your project starts accumulating **external knowledge** — interviews with potential users, articles about competitors, user research results, domain literature — add the knowledge module.

This is Andrej Karpathy's "LLM wiki" method in simplified form: you drop raw sources into a `raw/` folder, the LLM compiles them into linked wiki pages with cross-references.

### When to connect it

- You have at least 5 user interviews or discovery calls
- Or you're collecting industry / competitor research
- Or you're working on a product with a non-trivial domain where you need to accumulate terms and concepts

Before that — don't connect it. For a pet project "todo list for myself" it's overkill.

### Especially useful for solo and MVP

The knowledge module is **automation that actually works in dialogue**. You drop 3 interviews into `raw/`, tell Claude "ingest" — get a compiled persona in 5 minutes. For solo devs without an analyst team, this saves hours of manual synthesis.

### Simplified structure

```
my-project/
└── notes/
    ├── decisions.md
    ├── todo.md
    ├── patterns.md
    ├── gotchas.md
    └── knowledge/               ← added on demand
        ├── raw/                 ← everything here: transcripts, articles, notes
        │   ├── 2026-04-10-user-interview-alice.md
        │   ├── 2026-04-12-competitor-notion-pricing.md
        │   └── 2026-04-15-podcast-on-plg.md
        ├── wiki/                ← Claude compiles here
        │   ├── personas.md
        │   ├── competitors.md
        │   └── concepts.md
        └── AGENTS.md            ← Claude's maintainer instructions
```

No subfolders in raw/ — flat. For solo, classification by type is overkill.

### Minimal AGENTS.md for knowledge

Put this in `notes/knowledge/AGENTS.md`:

```markdown
# Knowledge wiki

## Your role
You maintain wiki/ as the maintainer.
Humans put raw sources into raw/, you compile into wiki/.

## Principles
1. raw/ — immutable. Never edit.
2. wiki/ — your zone. Create and update.
3. Every statement in wiki/ must reference a raw source.
   No source — don't write it.
4. Direct quotes — verbatim from raw/, no paraphrasing.
5. Contradictions — surface in a "Contradictions" section,
   don't smooth them over.

## User commands

### "ingest" or "ingest <file>"
Read new raw files. Update wiki. Report what you did.

### "what do we know about <X>"
Search in wiki/. Answer with quotes and links.
If not in wiki/ — check raw/. Don't make things up.

## Limits
- Don't edit wiki/ for stylistic reasons.
  The wiki reflects sources, not your literary taste.
- Don't delete wiki pages. Mark deprecated in frontmatter.
```

This is the minimum. If you want a more elaborate command set (contradiction checks, full maintainer instructions) — template in `knowledge.md` section 4.2. For solo mode, even the minimum works.

### Workflow

1. Over the week, you drop transcripts, articles, notes into `knowledge/raw/`. One file per item with a date in the name.
2. When a few have accumulated — run Claude Code in the project root:
   ```
   Read notes/knowledge/AGENTS.md.
   Ingest all new files in raw/. Update wiki.
   Report back.
   ```
3. Claude compiles; you look at the result in `wiki/`.
4. Check 2–3 quotes against the sources (hallucinations happen). If there are errors — tell Claude "this part is wrong, re-verify".
5. From there — you can ask questions: "what do we know about enterprise users?", "how do competitors handle pricing?". Claude goes into wiki/, returns a synthesis.

**Honest warning:** run ingest regularly. If the wiki isn't being updated but raw keeps growing — you live in an illusion of knowledge. Details in `knowledge.md` section 8.

---

## What NOT to do

### Don't create folders in advance

You don't need `notes/security/`, `notes/testing/`, `notes/deployment/`. Empty folders are psychological ballast. When real need appears — you'll add them.

Same for knowledge/ — don't create it until you have 5+ raw sources.

### Don't write ADRs

For a team of 1–2, ADRs are overkill. A one-liner in `decisions.md` with a date does the same work 10x faster.

### Don't do daily standup with yourself

It's silly, but people try. If you're alone — you know what you're doing. If you're two — Slack/Discord works without a ritual.

### Don't automate too early

No Cowork scripts generating weekly digests. You don't have a weekly — you have "code and TODO". Automation is needed when there's something to automate.

Exception: knowledge ingest, but it's in dialogue mode, not background.

### Don't copy the structure of large teams

Saw in some blog post: `domain/`, `product/`, `engineering/`, `design/`? Ignore it. That's for teams of 5+. For you it's a graveyard of empty folders.

---

## When to switch to the full version

One of three signals:

1. **You hire a third person.** Verbal explanations stop working, you need readable context.
2. **The project is in production and you have users.** Incidents, feedback, metrics appear — doesn't fit in four files.
3. **`decisions.md` or `patterns.md` grew past 300 lines.** Time to split by theme.

Before that, don't complicate things. The lite version pays off from the first week. The full one — only from month 3 with discipline. For a small project the choice is obvious.

When switching to the full version: the knowledge module, if already set up, moves into the repository as `knowledge/`. But not quite "just moves": in lite the `raw/` structure is flat, in the full version it has subfolders (`user-interviews/`, `competitors/`, `industry/`, `calls/`). You'll need to sort existing raw files into subfolders — usually 30 minutes of work for ~30 files. Wiki/ can stay as is — the LLM will recompile on the next ingest.

---

## First day example

You decided to start a new project. Idea — SaaS for habit tracking.

**Minutes 1–5.** Created repo, `npm create next-app`, opened in editor.

**Minutes 6–10.** Created `notes/` folder with two files: `decisions.md`, `todo.md`. In `decisions.md` wrote:

```markdown
# Decisions

## 2026-04-18
- Next.js 15 App Router (I know it best)
- Supabase (auth + DB + storage in one)
- Tailwind + shadcn/ui
- Deploy on Vercel
- TypeScript strict
```

**Minutes 11–15.** Created `AGENTS.md` in the root with references to notes/. Started Claude Code: "Scaffold the basics: auth via Supabase, layout with navbar, pages /, /login, /dashboard."

Claude read notes, used your decisions, generated code. You work without explaining for the fifth time that Supabase is used.

**One week in.** Got burned by Next.js App Router + Supabase SSR cookies — spent half an hour debugging. Once resolved, created `notes/gotchas.md` with one entry. In the next Claude Code session, it won't go down the same path again.

**One month in.** Started discovery calls with potential users. After the fifth one, created `notes/knowledge/raw/` and dropped all 5 transcripts there. Asked Claude to ingest — got the first version of a persona and three concepts users kept insisting on. Use this when planning the next features.

---

## Final

That's all. Two files at start, one AGENTS.md, 15 minutes of setup. Then — just add as you go. The other two files appear on demand (pattern on third copy / first gotcha). The knowledge module — when external sources accumulate.

If you didn't write anything in notes after a month — probably your project is very simple and even this isn't needed. If after a month notes/ became the main reference you return to — great, it works.

Complicate on actual pain, not on a plan.
