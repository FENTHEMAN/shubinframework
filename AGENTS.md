# AGENTS.md

Rules for AI agents (Claude Code, Codex, Cursor, and others) working on
this repository.

## What this repo is

Shubin Framework — a documentation-only repository describing conventions
for treating a markdown repo plus GitHub Issues as shared team context.

There is no code, no build, no tests. The "code" is markdown. The "build"
is rendering on GitHub.

A consequence is that most AI-agent skills in this repo are about
*editing prose*, not about implementing features. The author voice is
direct and honest about caveats; preserve it.

## What you read first

1. `README.md` — project overview and reading router.
2. `ARCHITECTURE.md` — repository layout and invariants.
3. `framework.md` — the main spec.
4. `decisions/` — accepted decisions, especially recent ones (this
   directory does not exist yet; when it does, read it).

Before suggesting any structural change, read the Invariants section of
`ARCHITECTURE.md`. Many "improvements" violate them.

## What you do

- Propose changes to spec documents (`framework.md`, `lite.md`,
  `knowledge.md`, `non-tech.md`, `non-engineering.md`) when asked.
- Help write new ADRs when the user describes a decision worth recording.
  Use the format from `framework.md` chapter 4.2.
- Update cross-references when files move, when chapters renumber, or
  when a section is renamed.
- Keep the Russian translation in `i18n/ru/` in mind: a change in a root
  spec document creates drift. Update `i18n/ru/` in the same PR or note
  the drift in the PR description.

## What you don't do

- Don't add a `features/` directory. Features live in GitHub Issues.
  This is one of the strongest invariants in `ARCHITECTURE.md`.
- Don't add a `knowledge/` directory in *this* repository. The framework
  prescribes `knowledge/` for adopting teams; we deliberately do not
  apply the framework to its own meta-work. The example AGENTS.md for
  the knowledge module lives in `templates/knowledge/AGENTS.md`.
- Don't add stateful frontmatter (status, owner, priority, due date) to
  any document. State lives in GitHub.
- Don't reorganise the top-level layout without an explicit instruction.
- Don't translate to other languages unless asked. Russian lives in
  `i18n/ru/`; other languages would add `i18n/<lang>/` plus a
  `README.<lang>.md` at root, but only on request.
- Don't add emoji to canonical (UPPER_CASE) documents. Lower-case spec
  documents may contain emoji only inside quoted external sources.
- Don't push to `main` during the v2 migration. All work goes on the
  `v2` branch with a `v2:` commit prefix; see
  `conventions/GIT_CONVENTION.md`.

## ADR style

When writing an ADR, use the format from `framework.md` chapter 4.2:

- `# ADR-NNNN: <short decision title>`
- `## Status` — `Accepted — YYYY-MM-DD`, or `Superseded by ADR-NNNN`
- `## Context` — situation and constraints
- `## Options considered` — pros and cons for each, including rejected
- `## Decision` — chosen option and a sentence on why
- `## Consequences` — what we lose, what we gain, what becomes harder
- `## Related` — links to other ADRs and to the GitHub Issue, if any

ADRs are immutable after acceptance. To change one, write a new ADR with
status `Supersedes ADR-NNNN` and update the older ADR's status to
`Superseded by ADR-NNNN`.

## Markdown style

- English in root spec documents; Russian only in `i18n/ru/`.
- Prose over bullet lists where reasonable. Bullets are for genuine
  enumerations, not for breaking sentences across lines.
- Backticks for filenames, paths, and identifiers.
- No emoji in canonical documents.
- Match the author's voice: direct, willing to call out where a practice
  fails, honest about caveats. Avoid corporate hedging and AI-slop
  ("furthermore, it is important to note that...").

## Templates

When the framework prescribes a concrete file an adopting team copies,
the canonical version lives in `templates/`:

- `templates/.github/ISSUE_TEMPLATE/` — Issue Templates for code repos.
- `templates/knowledge/AGENTS.md` — auto-ingest spec for the optional
  knowledge module.

If `framework.md` introduces a new copy-target, add it under `templates/`
rather than embedding the full file in the spec; the spec links to the
template.

## When in doubt

Ask before destructive operations: deleting files, moving across
directories, large prose rewrites. When uncertain about a convention,
check the Invariants in `ARCHITECTURE.md` first; if the answer is not
there, ask the user rather than guessing.
