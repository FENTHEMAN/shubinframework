# knowledge/AGENTS.md

Specification for the AI agent that maintains `knowledge/wiki/`.

This file describes how the wiki is compiled. The wiki itself
(`knowledge/wiki/*.md`) is the agent's writing space — it can be
regenerated from sources at any time without information loss.

This file is a *template* shipped with Shubin Framework. Copy it to the
markdown repo of a team that adopts the optional knowledge module
(`knowledge/AGENTS.md` in their repo), then adapt it: in particular, fill
in the linked code repositories in `knowledge/wiki/_meta.md` and trim
ingest sources the team does not use.

## Inputs (what to ingest)

On each compile run, the agent reads:

1. **`knowledge/raw/`** — all files. Source of truth for external
   material (articles, interviews, competitor analysis). Immutable;
   detect new files since the last run by filename date prefix
   (`YYYY-MM-DD-<slug>.md`).
2. **`decisions/`** — all ADRs. Detect new or modified files since the
   last run via `git log` (the last run date is recorded in
   `knowledge/wiki/_meta.md`).
3. **`journal/`** — all entries (`feedback/`, `incidents/`, `retros/`).
   Same detection method.
4. **GitHub Issues and PRs** in linked code repositories — closed Issues
   and merged PRs since the last run. Use the `gh` CLI or the GitHub
   API. The list of linked code repos lives in
   `knowledge/wiki/_meta.md`.
5. **`patterns/`** — for cross-references. When a wiki page discusses a
   pattern, link to the pattern file rather than restating it.
6. **GitHub Project items** if a Project is linked (Project id and field
   schema in `_meta.md`). The Project's `Affected parts` field is the
   primary classification signal — see "Project integration" below.

## Outputs (what to produce)

The wiki is organised by **product domain**, not by source type and not
chronologically.

Each domain has one page: `knowledge/wiki/<domain>.md`. Domain boundaries
are inferred from the Project's `Affected parts` field if available,
otherwise from Issue labels, ADR titles, and feedback clusters.
Examples: `auth.md`, `billing.md`, `onboarding.md`, `notifications.md`.

**Optional second orientation: discovery pages.** When the team also runs
discovery work (interviews, competitor analysis, industry research),
secondary pages live in `knowledge/wiki/personas/`,
`knowledge/wiki/competitors/`, etc., following the format described in
`knowledge.md` chapter 3. They share the same compile mechanism but the
sources are mostly `raw/`, not Issues. Both orientations may coexist.

The agent does **not** create:

- A page per Issue (use GitHub for that — the closed Issue is the
  record).
- A chronological log (use `journal/`).
- A page per ADR (link to the ADR file directly).

## Page format

Each `knowledge/wiki/<domain>.md` follows this structure:

````markdown
# <Domain name>

**Last compiled:** YYYY-MM-DD
**Sources:** N raw files, M ADRs, K closed Issues, L journal entries

## Overview

Two or three paragraphs synthesising what the team knows and decided
about this domain. The agent's own prose, drawing from sources.

## Key decisions

- [ADR-0024: OAuth library choice](../../decisions/0024-oauth-library.md) — short summary
- [ADR-0031: Token rotation strategy](../../decisions/0031-token-rotation.md) — short summary

## Active issues and recent work

- [#142: SSO via Google](https://github.com/your-org/your-repo/issues/142) — closed YYYY-MM-DD
- [#198: 2FA enforcement](https://github.com/your-org/your-repo/issues/198) — closed YYYY-MM-DD

## External knowledge

- [[../raw/2025-11-12-oauth-vs-oidc-deep-dive]]
- [[../raw/2026-01-04-customer-interview-auth-pain]]

## Feedback and incidents

- [[../../journal/feedback/2026-02-15-customer-x-sso-blocker]]
- [[../../journal/incidents/2026-03-01-token-leak-postmortem]]

## Open questions

- (Things not yet decided that the agent identifies as gaps.)
````

## Compile cadence

Default: **weekly**, e.g. every Monday morning. Trigger an ad-hoc run on
major events: a large release, a significant incident, a batch of new
ADRs.

The mechanism — a scheduled Claude Code session, a GitHub Action calling
`claude --headless`, or a human running it manually — is not part of the
framework. Only the input/output contract is.

## Commits

The agent commits to a branch (e.g. `wiki/auto-ingest-YYYY-MM-DD`) and
opens a PR titled `wiki: weekly compile YYYY-MM-DD`. The PR body
summarises:

- Domains updated.
- New sources ingested (counts and a few representative filenames).
- Decisions added or marked superseded.
- Feedback and incidents folded in.

A human reviews and merges. The agent does not push to `main` directly.

## What the agent does NOT do

- Does not paraphrase ADRs into prose summaries that could drift from
  the original. Always link to the ADR file.
- Does not invent information not in any source.
- Does not delete content without explicit instruction; if a domain page
  becomes obsolete, flag it for human review.
- Does not edit `knowledge/raw/`. Raw is immutable.
- Does not edit ADRs. ADRs are immutable after acceptance.
- Does not edit `journal/` entries. Journal is append-only.

## Meta file

`knowledge/wiki/_meta.md` records:

- Last compile date.
- List of linked code repositories (for Issues/PRs ingest).
- List of domains and their corresponding wiki pages.
- Known issues with the wiki (gaps, conflicts) flagged for human
  follow-up.

## Project integration

When a GitHub Project is linked (id and field schema in `_meta.md`), the
agent uses the Project's custom fields as primary classification signals
— overriding label and title heuristics:

- **Affected parts** (multi-select) — the strongest signal. An Issue
  with `Affected parts: [auth, billing]` updates both `auth.md` and
  `billing.md`.
- **Type** (single-select) — separates Feature, Bug, Tech debt, Spike,
  Doc. The page format above can grow per-Type subsections when useful.
- **Status** — only items in `Done` are folded into the wiki narrative;
  closed-as-`wontfix` items are skipped.
- **Iteration** — used when the agent is asked for "what landed in
  iteration N" or "since iteration N".

Reading the Project via `gh`:

```
gh project item-list <number> --owner <org> --format json --limit 200 \
  --jq '.items[] | {title: .content.title, status: .Status,
                    parts: .["Affected parts"], type: .Type}'
```

For larger Projects, paginate or use GraphQL directly.

The agent **does not** modify the Project's structure — fields, views,
workflows are human decisions. The agent may add closed Issues from
linked repos to the Project if the auto-add workflow is missing them,
and may update per-item field values when explicitly asked. Anything
beyond that requires a human action.

Without a linked Project, the agent falls back to: Issue labels, linked
PR file paths, journal entry tags, ADR title keywords. Domain assignment
misses ~10–20% of items in this fallback mode. See `knowledge.md`
chapter 12 for the full integration story and `projects.md` for the
Project field schema.

## Human override

Humans may edit `knowledge/wiki/*.md` directly when the agent
misclassifies or hallucinates. After a human edit, add a one-line note
to this `AGENTS.md` clarifying the rule, so the next compile run
respects it. Treat such notes as the way `knowledge/AGENTS.md` evolves;
do not patch symptoms in the wiki itself.
