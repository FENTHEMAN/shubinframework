# Shubin Framework: Knowledge Module

**Full title:** LLM wiki for teams, adapted from Karpathy's method
**Part of:** Shubin Framework (companion document)
**Version:** 1.0

**Who it's for:** any team member — developer, PM, designer, CEO, domain expert. No technical background required. If you can write markdown and click commit in GitHub Desktop — that's enough.

**What it is:** a framework module implementing Andrej Karpathy's "LLM wiki" method for team knowledge management. The LLM (Claude Code or Cowork) doesn't just read your knowledge base — it **actively maintains it**: compiles raw sources into linked pages, updates cross-references, flags contradictions.

**What it isn't:** not a replacement for ADRs, not a replacement for feature specs, not a task board. The knowledge module is only responsible for **external and domain knowledge**: user interviews, competitors, personas, industry trends, research.

**Related documents:**
- `framework.md` — full framework
- `lite.md` — simplified version for solo/MVP
- `non-tech.md` — how to work with git without the terminal

---

## Before you start: what this is and why

### The problem this module solves

In a team, **raw knowledge about the external world** accumulates:
- Transcripts of user interviews
- Articles about competitors
- Podcasts about the industry
- Research reports
- Client calls
- Screenshots of other products

It all sits in Google Drive, Notion, browser bookmarks, Slack chats. Three months later:
- Nobody remembers which interview had that key user quote about pricing
- Competitor X and Y blur into "some competitor" in everyone's head
- Everyone has their own version of the "small business owner" persona
- The same domain terms get re-explained to every new person

**The knowledge module solves this.** You drop raw materials into one folder. The LLM reads and compiles them into structured pages: personas, concepts, competitors, interview quotes. Pages are linked with `[[wikilinks]]`. When new sources arrive, the LLM updates existing pages and flags contradictions.

### Where the idea comes from

In April 2026, Andrej Karpathy published the "LLM wiki" pattern — a knowledge base maintained by the LLM itself. Three layers:

1. **raw/** — immutable sources
2. **wiki/** — LLM-generated pages with cross-references
3. **CLAUDE.md** — instructions for how the LLM maintains the wiki

For personal knowledge management, thousands of people are already using it. For teams, the approach adapts almost 1-to-1, with small caveats (below).

### How this differs from the rest of the framework

Shubin Framework has other layers:
- `active/decisions/` — ADRs (our decisions). Humans write.
- `active/features/` — feature specs. Humans write.
- `active/patterns/` — code patterns. Humans write.
- `log/feedback/` — raw append-only feedback. Humans write.

These are **human-maintained** layers. The LLM reads them, doesn't modify them.

The knowledge module is **the only layer where the LLM is the maintainer**. Humans drop sources, the LLM compiles. That's the fundamental difference.

---

## 1. Module architecture

### 1.1. Folder structure

```
vault/
├── stable/
├── active/
├── log/
└── knowledge/                    ← new layer
    ├── raw/                      ← immutable, humans write/drop
    │   ├── user-interviews/
    │   │   ├── 2026-04-10-acme-corp.md
    │   │   └── 2026-04-15-beta-startup.md
    │   ├── competitors/
    │   │   ├── 2026-04-02-notion-pricing-changes.md
    │   │   └── 2026-04-18-linear-blog-on-ai.md
    │   ├── industry/
    │   │   └── 2026-04-05-gartner-report-summary.md
    │   └── calls/
    │       └── 2026-04-12-client-discovery-X.md
    │
    ├── wiki/                     ← LLM-maintained
    │   ├── personas/
    │   │   ├── small-business-owner.md
    │   │   └── enterprise-buyer.md
    │   ├── concepts/
    │   │   ├── product-led-growth.md
    │   │   └── usage-based-pricing.md
    │   ├── competitors/
    │   │   ├── notion.md
    │   │   └── linear.md
    │   ├── themes/
    │   │   └── ai-in-productivity-tools.md
    │   └── index.md              ← wiki catalog
    │
    └── CLAUDE.md                 ← LLM maintainer instructions
```

### 1.2. Two rules you can't break

**Rule 1: raw/ is immutable.**
Once you put a file into `raw/`, you **do not edit its contents**. Found a mistake? Add a new file with a correction; don't touch the old one. This is critical: `raw/` is your "factual history", and if you edit it, the LLM starts compiling the wiki on shifting foundations.

**Rule 2: wiki/ is maintained by the LLM.**
You don't write into `wiki/` by hand, except in rare cases (fixing an obvious hallucination). Wiki pages are created and updated by the LLM on your commands. If you see something wrong in `wiki/personas/small-business-owner.md` — **fix the sources in raw/ or ask the LLM to update**, don't edit the wiki directly.

These two rules are the foundation. Breaking the first kills trust in the system. Breaking the second turns the wiki into a regular draft document, and the LLM no longer knows what it's doing.

---

## 2. What exactly goes into raw/

### 2.1. Source formats

Everything in markdown. If the source isn't in markdown, convert it before uploading:

| Source | How to convert |
|---|---|
| Zoom / Meet transcript | Copy into .md, add frontmatter |
| Blog article | Save as markdown (browser extensions, MarkDownload) |
| PDF report | Claude Code can read a PDF and create a markdown summary. Or use `pdftotext` and clean it up |
| Podcast transcript | Copy from transcription service (YouTube Premium, Otter.ai) |
| Competitor screenshots | Usually not needed in raw/ as-is. Better — a markdown note with description and a link to the screenshot |
| Email thread | Copy text, redact PII |
| Call notes | Write directly in markdown during the call |

### 2.2. Frontmatter for raw files

Minimum:

```markdown
---
type: user-interview     # user-interview | competitor | industry | call | other
source: Acme Corp        # company/source/name
date: 2026-04-10
author: @alice           # who uploaded to the vault
---
```

This helps the LLM understand context during compilation. Works without frontmatter too, but worse.

### 2.3. What to put in, what not to

**Put in:**
- Anything containing factual knowledge about the external world
- Primary transcripts and notes
- Articles, studies, reports

**Don't put in:**
- Your internal ADRs (those are in `active/decisions/`)
- Feature specs (those are in `active/features/`)
- Team operational documents (statuses, schedules)
- Third-party materials without PII scrubbing (client names, phone numbers, financial data)
- NDA-protected materials that can't be distributed inside the team

### 2.4. The "more is better" principle

In regular documentation, each document is a maintenance cost. In the knowledge module, **each raw source is an asset**. The LLM processes volume. 50 interviews give a qualitatively more useful wiki than 5. The more raw, the more accurate the personas and concepts.

Don't fear "cluttering" raw/. It isn't clutter — it's data.

---

## 3. What comes out in wiki/

### 3.1. Types of wiki pages

Four main types:

**Personas** (`wiki/personas/`). Compiled descriptions of user segments based on interviews and calls. What hurts, how they solve it now, what they say verbatim, what their workflow is.

**Concepts** (`wiki/concepts/`). Key ideas from the domain and industry. "What product-led growth is", "how usage-based pricing works", "what zero-party data is". One page = one concept.

**Competitors** (`wiki/competitors/`). Competitor profiles. Pricing, features, positioning, recent changes, their strengths and weaknesses. Compiled from articles, blogs, screenshots.

**Themes** (`wiki/themes/`). Trending topics from the industry. "AI in productivity tools", "shift to vertical SaaS", "consolidation in observability". A synthesis across several sources.

### 3.2. Wiki page format

Example: `wiki/personas/small-business-owner.md`:

```markdown
---
type: persona
last_updated: 2026-04-18
sources: 8
---

# Small Business Owner

## Characteristics
- 5–50 employees
- Makes tool decisions personally
- Budget: $50–500/mo per tool
- Technically literate at a basic level, but not a developer

## Pain points
- Fragmentation of tools: email, CRM, invoicing — all in different places
  (mentioned in [[raw/user-interviews/2026-04-10-acme-corp]], [[raw/calls/2026-04-12-client-discovery-X]])
- No time to set up complex systems
  (mentioned in [[raw/user-interviews/2026-04-15-beta-startup]])

## Quotes
> "I'm willing to pay more if it saves me an hour a week"
> — [[raw/user-interviews/2026-04-10-acme-corp]]

> "I tried Notion, HubSpot, Airtable. All too complex for us"
> — [[raw/calls/2026-04-12-client-discovery-X]]

## Related
- [[wiki/concepts/product-led-growth]]
- [[wiki/competitors/notion]]
- [[wiki/themes/tool-consolidation]]

## Contradictions / open questions
- [[raw/user-interviews/2026-04-10-acme-corp]] says "budget up to $100".
  [[raw/calls/2026-04-12-client-discovery-X]] says "ready to pay $500".
  Possibly two different sub-segments. Needs clarification.
```

### 3.3. What makes a good wiki page

- **Every claim references a raw source.** Without this, the page is a hallucination.
- **Quotes are verbatim from the interview.** No paraphrase, no reformulation.
- **Cross-links via [[wikilinks]]** — Obsidian's native link mechanism.
- **"Contradictions" section is explicit.** If sources disagree, it's marked, not smoothed over.
- **Updates when new sources arrive**, not rewritten from scratch.

---

## 4. The module's CLAUDE.md: maintainer instructions

### 4.1. Why a separate CLAUDE.md

You already have `COMMON.md` at the vault root. But `knowledge/` needs a **separate** `CLAUDE.md`, because the LLM's behavior here is different: not "read while working on code", but "actively maintain the wiki".

This file lives at `knowledge/CLAUDE.md` and is read by Claude Code / Cowork when working with this folder.

### 4.2. A working CLAUDE.md example

```markdown
# Knowledge module: maintainer instructions

## Your role

You maintain wiki/ as LLM-maintainer following Karpathy's method.
Humans put raw sources into raw/, you compile them into wiki/.

## Principles

1. raw/ — immutable. Never edit files in raw/.
2. wiki/ — your zone. Create and update pages there.
3. Every claim in wiki/ must reference a raw source
   via [[wiki-link]]. No source — don't write it.
4. Direct quotes — verbatim from raw/. Never paraphrase.
5. Contradictions — surface in an explicit "Contradictions"
   section, don't smooth them over.

## User commands and how to react

### "ingest <file> into knowledge"
1. Read the file in raw/.
2. Determine which wiki pages already exist and relate to the topic.
3. Update existing pages with new facts and quotes.
4. If new pages are needed (new persona, new concept,
   new competitor) — create them with the full set of sections.
5. Update index.md.
6. Report to the user: which pages were touched, which are new,
   which contradictions were surfaced.

### "what do we know about <X>"
1. Start by searching wiki pages on the topic.
2. If a page exists — answer in its terms, with quotes and links.
3. If no page exists but raw/ has something relevant —
   propose creating a wiki page.
4. Never invent facts that aren't in raw/ or wiki/.

### "update <page>"
1. Read all raw sources it references.
2. Read all new raw/ files since last_updated.
3. Update sections, preserve structure.
4. Update last_updated and sources count.

### "check wiki for contradictions"
1. Walk through all wiki pages.
2. Find claims that contradict each other across
   different raw sources.
3. Mark in the "Contradictions" section on the corresponding pages.
4. Return a short report to the user.

## Limits

- Don't create wiki pages silently. Report what you created.
- Don't delete wiki pages. If a page is outdated —
  mark "deprecated" in frontmatter and report.
- If raw sources contradict each other — don't pick
  "the correct one". Record both and flag.
- If the user asks to "rewrite" or "improve the style"
  of a wiki page — refuse. The wiki reflects sources,
  not your literary taste.
```

### 4.3. What's subtle here

Instructions are written as explicit "you", with concrete commands and triggers. This is not a general style guide. Claude Code follows such explicit instructions better than abstract principles.

Critically: **the prohibition on inventing facts and the obligation to reference sources**. Without this, the wiki turns into a hallucinated swamp within a week, and nobody trusts it.

---

## 5. Workflow

### 5.1. Weekly cycle

**Any day of the week:** you've dropped new raw sources from the past week. Interviews, calls, articles, reports.

**Ingest session (20–40 minutes, 1–2 times a week):**
1. Open Claude Code in the vault folder (or Cowork, if you prefer).
2. Command: "Go to knowledge/CLAUDE.md, read the instructions. Then ingest all new files in raw/ from the past week."
3. Claude reads each new raw file, updates the wiki.
4. After each file — git commit ("ingest: <file>"). This gives you a compilation history.
5. After the whole session — a short report from Claude: what was updated, what was created, which contradictions were found.

**Once a month:** run the "check wiki for contradictions" command. This surfaces accumulated inconsistencies across sources.

### 5.2. Ad-hoc queries

During the week, when someone needs knowledge:

- "What do we know about e-commerce users?" → Claude goes into wiki/personas/, returns a synthesis.
- "How does competitor X position pricing?" → Claude goes into wiki/competitors/, returns the answer.
- "Which users mentioned Slack integrations?" → Claude greps across raw/ and wiki/, returns quotes with links.

This is the main value of the module: the team gets access to shared knowledge in response to natural-language questions.

### 5.3. Who in the team drops what kind of source

| Role | What they typically upload |
|---|---|
| PM / Product | User interviews, discovery calls |
| Sales / Success | Client calls, their objections and wishlists |
| CEO / Founder | Investor conversations, industry interviews |
| Designer | Usability tests, UX industry trends |
| Developer | Technical articles, competitor releases |
| Marketer | Industry reports, competitor positioning |

Each is the owner of their raw/ category. Everyone together reads the wiki/.

### 5.4. When NOT to use the Knowledge Module

- **Your vault only contains technical decisions and ADRs.** Then the module isn't needed — it's for the external world, not internal decisions.
- **You have fewer than 10 raw sources in the first 2 months.** The module pays off on volume. Setting it up for three interviews is overkill.
- **The team isn't ready to run an ingest session once a week.** Without that discipline, the wiki will be falsely out-of-date, which is worse than no wiki.

---

## 6. Setup: step by step for all roles

### 6.1. Technical person (developer, tech lead): 20 minutes

1. In the vault repo: create the `knowledge/` folder with `raw/` and `wiki/` subfolders.
2. Create `knowledge/CLAUDE.md` based on the template in section 4.2.
3. Create `knowledge/raw/user-interviews/`, `raw/competitors/`, `raw/industry/`, `raw/calls/` — according to the team's actual needs.
4. Create `knowledge/wiki/index.md` with an empty list — the LLM will fill it in.
5. Commit + push.
6. Explain to the rest of the team where to drop raw (see 6.2).

### 6.2. Non-technical person (PM, designer, CEO): 15 minutes

This section assumes that your team already has Claude Code set up (on a developer's machine) and/or Cowork (desktop app for vault work). If not — ask a developer to set things up first. For non-tech contribution to raw/, either of the two is enough; the ingest session is usually run by a developer via Claude Code, but you can query the wiki via Cowork independently.

If you haven't set up git yet — start with `non-tech.md`, sections 2–3.

Then:
1. In your vault clone, open `knowledge/raw/<subfolder>/`.
2. Create a new file: `2026-04-18-<what-it-is>.md`.
3. Add frontmatter (template in section 2.2).
4. Paste the contents (transcript, article text, notes).
5. Commit in GitHub Desktop with message `raw: <what it is>`.
6. Push.

Some time later (on the next ingest session), Claude will update wiki/ itself. You can look at results in `knowledge/wiki/`.

If you need an urgent "what do we know about X" — ask a developer to run Claude Code with the query. Or connect Cowork and ask it yourself.

### 6.3. Running ingest for the first time

When 5–10 files have accumulated in raw/:

1. Open Claude Code at the vault root (developer).
2. Prompt:
   ```
   Read knowledge/CLAUDE.md. Then ingest all files
   in knowledge/raw/. Start with user-interviews/.
   After each wiki update, make a git commit with a
   short description.
   If you find contradictions across sources —
   mark them on the corresponding wiki pages.
   Share a plan before starting, and a report after.
   ```
3. Claude proposes a plan. Check if it looks right. Say "go".
4. In 5–15 minutes you get a report: "created X pages, updated Y, found Z contradictions".
5. Review wiki/. If something is clearly hallucinated — tell Claude "this statement is wrong, here's the right source".

---

## 7. Lite version of the module (for solo/MVP)

The full module for a team of 1–2 is overkill. Simplified version:

```
notes/
├── decisions.md
├── patterns.md
├── gotchas.md
├── todo.md
└── knowledge/                ← added when >5 raw
    ├── raw/
    │   └── *.md              ← flat, no subfolders
    └── wiki/
        └── *.md              ← flat
```

**Differences from the team version:**
- No subfolders in raw/ — everything flat. For solo, classifying by type (interview/article/call) is overkill.
- wiki/ is flat too — 5–10 pages, not a hierarchy.
- CLAUDE.md for knowledge/ is a copy of the template from 4.2, minus commands like "check for contradictions" — redundant for 5–10 pages.
- Ingest — every 2–3 weeks, not weekly.

**When to add the module to lite:**
- You started doing discovery interviews or user research
- 5+ raw sources have accumulated that you want to "pull together"
- You're working on a product with a non-trivial domain, and you need a base of terms/concepts

Before that — don't add it. The four base files of the lite version are enough.

**Why this matters for lite.** For pet projects or MVPs, the ingest loop through Claude is **fully automated**. You drop 3 interviews into `raw/`, say "ingest" — get a compiled persona in 5 minutes. You don't have to sit down and write a summary by hand. For solo, this saves hours.

---

## 8. Honest warnings

### 8.1. LLMs hallucinate

Even with an "always reference the source" instruction, Claude sometimes:
- Attributes a quote to the wrong interview
- Makes a generalization that isn't in the sources
- Creates a link between sources that's superficial

**Antidote:** the rule **a human reads every wiki page after first creation**. Then — on significant updates. Check 2–3 quotes against sources. If there are hallucinations — tell Claude "this is wrong, re-verify".

### 8.2. The wiki silently goes stale

Sources get added, but ingest doesn't run → the wiki becomes stale. Signal: `last_updated` in frontmatter older than 2 weeks while the team is actively working.

**Antidote:** the ingest session is a ritual, not "we'll do it when there's time". Once a week, 30 minutes, regardless of how many new sources there are. If none — Claude will say "no new sources", that's fine.

### 8.3. The temptation to edit the wiki by hand

A person sits down, sees a wrong sentence in `wiki/personas/small-business-owner.md`. Thinks: "let me just fix it". A month later, half the wiki is hand-edited, and LLM-compilation no longer matches reality.

**Antidote:** if something in the wiki is wrong — **find the raw source** that produced it, fix it (or add a correction file to raw/), run ingest. Or explicitly ask Claude: "on page X claim Y is wrong, re-verify and update". Never edit the wiki directly.

Exception: if Claude hallucinated something *absent from the sources* — delete it by hand and record it in the commit message. That's debugging, not content editing.

### 8.4. This is not RAG or search

Expectations the module does NOT fulfill:
- It doesn't answer "find all mentions of X in raw/" in milliseconds (use grep for that)
- It doesn't generate SQL-like queries over sources
- It doesn't replace semantic search across a large document corpus

The knowledge module is **compilation, not retrieval**. The value is not in fast search but in knowledge synthesized into readable pages with sources.

### 8.5. No automatic re-compile

When you add a raw file, the wiki **isn't updated automatically**. You must explicitly run ingest. You could set up a GitHub Action that runs ingest on each push — but that's automation on top, not part of the base module. Don't promise the team "just drop a file and the wiki updates itself" if you haven't set up automation.

---

## 9. Integration with the rest of the framework

### 9.1. Knowledge → ADR link

ADRs can reference wiki pages: "based on the needs of [[wiki/personas/small-business-owner]], we choose X". This is honest justification, traceable back to real interviews.

The ADR template can include an optional **Research context** section:
```markdown
## Research context
- Persona: [[wiki/personas/small-business-owner]]
- Competitor benchmark: [[wiki/competitors/notion]]
```

### 9.2. Knowledge → feature link

In a feature page, the Why section can reference wiki personas instead of vague target-audience descriptions:

```markdown
## Why
This feature closes a pain point from [[wiki/personas/small-business-owner]]:
"tool fragmentation".
Success criteria validated on the interview
[[knowledge/raw/user-interviews/2026-04-10-acme-corp]].
```

### 9.3. Knowledge → feedback loop link

When new user signals appear in `log/feedback/` (tickets, reviews, usage observations), they can be periodically promoted to `knowledge/raw/` if they carry domain knowledge, not a specific bug report.

Distinction:
- `log/feedback/` — operational stream (ticket, review, observation)
- `knowledge/raw/` — material for compilation into knowledge about the world

Not every feedback item makes it to knowledge. A human decides (usually the PM).

---

## 10. Final

The knowledge module is **the only Shubin Framework layer where the LLM doesn't just read but maintains**. This is closest to the original Karpathy idea, and this is where the AI agent delivers an effect that's hard to get by hand: **compilation at scale**.

If you have a team doing a lot of discovery work (interviews, research, competitive intel) — the module pays off at 10–20 raw sources. If not, don't turn it on. An empty or half-empty knowledge layer is worse than none: it creates an impression of knowledge that doesn't exist.

**Discipline that actually works:**
1. Put raw in honestly — raw content, no "pretty/ugly" filtering.
2. Run ingest on schedule, not by mood.
3. Read the wiki with your own eyes at least once a month, check for hallucinations.
4. Never edit the wiki by hand (with rare debug exceptions).

---

*The module is based on Andrej Karpathy's "LLM wiki" method, original gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f. In Shubin Framework, the module is adapted for team use and integrated with the rest of the architecture.*
