# Architecture

## Bird's eye view

This repository contains the Shubin Framework — a set of conventions for treating a markdown repository as shared team context for humans and AI agents. The repo is itself documentation: there is no compiled artifact, no runtime, no tests. The "code" is markdown, and the "build" is rendering it on GitHub.

The framework targets teams of two to eight people who use AI agents like Claude Code as regular contributors and need a single place where decisions, features, knowledge, and external research live. It addresses two pains that show up together: AI agents start every session from scratch and forget what was decided yesterday, and humans three months later cannot remember why the API returns camelCase. The framework's claim is that both are the same problem — context lives in heads instead of in a readable form — and that a markdown repository read equally by humans and AI agents is the cheapest fix.

This document is for contributors and AI agents working *on* the framework itself, not for teams adopting it. It maps where things live in this repository and the invariants we hold. For a tutorial on using the framework, read `framework.md`; for the elevator pitch, `README.md`; for the solo or two-person variant, `lite.md`.

Two principles run through every choice in this repo and in the framework it documents. *Append-only for history, mutable for state*: decisions, feedback, and incidents are only appended to; roadmaps, feature pages, and ADR statuses change. *One source of truth per fact*: each fact has exactly one canonical home, and the layout exists to make those homes obvious. Most of the invariants below are direct consequences of these two principles; treat them as the cheap way to keep the principles true.

The repository is in the middle of a v1 → v2 migration. Where the current layout differs from the target layout, this document describes the current state and notes what is pending. The migration is tracked outside this repo and is not applied to the repo itself: there are no ADRs or feature pages for the framework's own work.

## Repository layout

```
README.md              English entry point and elevator pitch
README.ru.md           Russian entry point (mirror of README)
ARCHITECTURE.md        this file — map of the repo for contributors
CONTRIBUTING.md        how to propose changes
CHANGELOG.md           release history
LICENSE                MIT

framework.md           full specification, for tech leads
lite.md                solo and 1–2 person variant
knowledge.md           Karpathy LLM-wiki module specification
non-tech.md            git and the repository without the terminal
non-engineering.md     Finance / Legal / Security / Data / Support minimum

conventions/           cross-cutting conventions for contributors
  GIT_CONVENTION.md    commit and branch rules

i18n/                  translations of the lower-case spec documents
  en/                  English mirrors (currently duplicates the root)
  ru/                  Russian translations
```

The lower-case files at the root are the framework itself — the documents an adopting team reads. The UPPER_CASE files are meta-documents about this repository. `CONTRIBUTING.md` and `CHANGELOG.md` were added in stage 4 of the v2 migration. The remaining canonical UPPER_CASE files anticipated by v2 — `STACK.md`, `GLOSSARY.md`, `AGENTS.md` — are still absent and will land in a later stage; this section is updated as they do.

### `README.md`, `README.ru.md`

The entry points. `README.md` is the canonical English version; `README.ru.md` mirrors it in Russian. A non-English visitor must be able to find the Russian README without descending into a subdirectory, which is why these two files break the otherwise-strict "translations live in `i18n/`" rule.

### `ARCHITECTURE.md`

This file. It exists so that a contributor or AI agent landing in the repo for the first time has a single short document that explains the layout and the invariants. It is the only meta-document about the layout; if a rationale is not here, it is in `framework.md` or in a commit message.

### `CONTRIBUTING.md`

The contributor entry point: how to propose a change (issue first for anything beyond a typo), what kinds of contributions are welcome (counterexamples, real cases, reports of practices that didn't work), what needs discussion before a PR (new top-level directories, new canonical files, chapter renames), and the project's editorial style. Read this before opening a non-trivial PR.

### `CHANGELOG.md`

Release history in [Keep a Changelog](https://keepachangelog.com/) format. The `[Unreleased]` block accumulates v2 work in progress; the v2.0.0 entry is filled in at release. Until then, the per-stage `v2:` commits on the `v2` branch are the authoritative log of what changed.

### `LICENSE`

MIT. The framework is given away; adopting teams are expected to copy or fork the structure freely. The license has not changed since v1 and is not expected to change in v2.

### `framework.md`

The full specification of the framework, for the tech lead setting up a repository from scratch. Covers the two principles, the directory layout an adopting team creates, the lifecycle of features and ADRs, the role of the journal, scaling guidance, and the optional Karpathy module. Roughly five hundred lines as of v2; the running onboarding example was extracted to `guides/feature-cycle-example.md`.

### `lite.md`

The solo and 1–2 person variant. For an MVP or pet project where the full framework would be overhead, `lite.md` keeps the same principles but drops the ceremony. A team of one with `lite.md` should still be able to graduate to `framework.md` later without rewriting their notes.

### `knowledge.md`

The Karpathy LLM-wiki module specification. Three layers — immutable raw sources, an LLM-compiled wiki with cross-references, and an `AGENTS.md` that tells the LLM how to maintain the wiki. This is an *optional* module: a team can adopt the rest of the framework without it. The split into a separate document reflects that optionality.

### `non-tech.md`

For PMs, designers, and CEOs who need to read and edit the repository without using the terminal. Covers the GitHub web UI flow, branch protection, and review etiquette for non-engineers. Read this if you are setting up a repository that includes non-technical contributors, or if you are a non-engineer trying to participate in one.

### `non-engineering.md`

The minimum a founder needs for Finance, Legal, Security, Data, and Support. Each domain gets a short section with the questions a founder will be asked and where in the repository the answers live. Stack-agnostic and intentionally shallow — it is a starting point, not a manual.

### `conventions/`

Cross-cutting conventions for contributors to this repo. Today this is just `conventions/GIT_CONVENTION.md`, which prescribes commit message format and branch naming — including the v2-specific rule that all migration commits go on the `v2` branch with a `v2:` prefix and atomic scope. As more cross-cutting rules accrete (style guide for prose, review etiquette, release process), they belong here too rather than as new top-level files; the root is reserved for canon and for framework specs.

### `i18n/`

Translations of the lower-case spec documents. `i18n/ru/` carries the Russian translations and is the active translation directory; an editor changing a root spec is expected to either update `i18n/ru/` in the same PR or open a follow-up issue noting the drift. `i18n/en/` exists for symmetry but currently duplicates the root verbatim — a transitional artifact of the v1 layout, likely to be removed in a later v2 stage. The canonical English text is always the root copy, never `i18n/en/`.

## Document hierarchy

Files in this repo fall into four layers, distinguished by their place and case.

### UPPER_CASE in root = canon

Documents whose names a contributor or AI agent should learn first: `README.md`, `ARCHITECTURE.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `LICENSE`. Future canon files still pending are `STACK.md`, `GLOSSARY.md`, and `AGENTS.md`. They change rarely and describe what this repository *is* rather than what the framework prescribes. The shouty-case is deliberate: a contributor scanning the root should be able to spot the meta-documents without reading filenames carefully.

### lower-case in root = framework specs

The documents the framework consists of, each targeting a different audience: `framework.md`, `lite.md`, `knowledge.md`, `non-tech.md`, `non-engineering.md`. These are the *product*; they evolve as the framework does. Each document is meant to be read against a concrete task, not cover-to-cover. A tech lead reaches for `framework.md`; a solo founder for `lite.md`; a PM for `non-tech.md`.

### `conventions/` = cross-cutting rules

Conventions that apply across the whole repo rather than to one specific document. They are meta-rules about *how this repository is maintained*, not part of the framework's user-facing specification.

### `i18n/<lang>/` = translations

Mirrors of the lower-case specs in another language. The root-level `README.<lang>.md` is the only translation that lives at the top level, because the README is the entry point and a non-English-speaking visitor must hit it without descending into a subdirectory.

### What the framework itself prescribes (not present here)

The framework — described in `framework.md` and `lite.md` — prescribes additional directories that adopting teams create in *their* repos: `decisions/` for ADRs, `patterns/` for engineering patterns, `journal/{feedback,incidents,retros}/` for the append-only chronicle, `knowledge/{raw,wiki}/` with `knowledge/AGENTS.md` for the Karpathy module, and `guides/` for long-form examples. None of those exist in this repo, and none will: per project policy we do not apply the framework to its own meta-work.

Notably absent from that list — and absent in adopting teams' repos too — is a `features/` directory. In v2 features live in GitHub Issues, not in markdown. The Issue is the spec, the tracker, and the conversation; structure for P0/P1 features is enforced by an Issue Template rather than by a markdown skeleton.

The reason for the not-applying-to-itself rule is that the framework is a moving target during v2, and using a moving target to track its own movement creates circular references and unhelpful churn. Once v2.0.0 ships, a future version may revisit this — but the bar for revisiting it is the framework being stable enough to dogfood.

## Invariants

Many of these are stated as the *absence* of something rather than the presence — matklad's observation that invariants are often best expressed negatively. The list below is what a contributor must keep true when editing this repo.

- No `vault/` directory. v2 deliberately flattened. Earlier drafts wrapped every working directory in `vault/`; the v2 migration lifts them to root because GitHub and the filesystem already provide the wrapper, and a redundant prefix on every path adds noise without information.
- No `COMMON.md`. AI-agent instructions live in `AGENTS.md` (root) and `knowledge/AGENTS.md` (Karpathy module). `AGENTS.md` is the cross-tool standard adopted by Claude Code, Cowork, and others; we follow it rather than invent our own filename.
- No nested subfolders under `decisions/`. ADRs are flat and sequentially numbered (`0001-...md`, `0002-...md`, ...). Numbering never resets, even after major rewrites of the framework or of an adopting team's repository. The flat numbering is the index; categorisation happens through tags inside the document, not through directories.
- **No `features/` directory** — not in this repo, not in adopting teams' repos. Features are GitHub Issues, not markdown documents. A markdown feature page that mirrored issue state would diverge from the issue within days; one without state would duplicate the issue's body. The v2 design closes the dilemma by removing the markdown layer entirely. Markdown holds knowledge (ADRs, patterns, retros, wiki); GitHub holds workflow (features, milestones, status, owner, priority).
- No emoji in canonical (UPPER_CASE) documents. Lower-case spec documents may contain emoji only where a quoted external source has them.
- Each lower-case spec is self-contained for its audience. A solo founder reading `lite.md` should not need to read `framework.md` to apply it; a tech lead reading `framework.md` should not need `intro.md`. Cross-references between specs are allowed, but a reader who follows zero of them should still get a working picture.
- English in roots; Russian only in `i18n/ru/`. The canonical text is English; translations follow. A change in the English root is the source of truth; the Russian translation may lag and is expected to.
- `knowledge/raw/` is immutable. A new version of an external source is a new dated file (e.g., `paper-2026-04-17.pdf`), never an in-place edit of the previous file. Editing in place would silently invalidate any wiki page that cited the earlier version.
- `knowledge/wiki/` is maintained by an AI agent following `knowledge/AGENTS.md`. Routine updates come from auto-ingest of `knowledge/raw/`, closed Issues, merged PRs, new ADRs, and `journal/` entries. Humans edit the wiki only to correct mistakes; if hand-editing is recurring, fix `knowledge/AGENTS.md` rather than patching symptoms.
- The framework is not applied to its own repository. No `decisions/0001-v2-restructure.md`, no ADRs or feature artefacts for v2 work itself. Meta-work for the framework is tracked in commit messages and PR descriptions. This is a deliberate choice during the v2 migration; it may be revisited in a later version.
- During the v2 migration, no commits go directly to `main`. All work lands on the `v2` branch with the `v2:` commit prefix and is merged in one piece when v2.0.0 is ready. This invariant disappears once v2 ships.
- No translation lives outside `i18n/<lang>/`, except the language-specific READMEs at the root. A new translation language adds an `i18n/<lang>/` directory and a root-level `README.<lang>.md`; it never adds a sibling tree at the root.

## Where to find things

| Looking for                                  | Read                                |
|----------------------------------------------|-------------------------------------|
| What this is, who it's for                   | `README.md`                         |
| How the repo is organized (this file)        | `ARCHITECTURE.md`                   |
| The framework, full version                  | `framework.md`                      |
| The framework, solo or pair version          | `lite.md`                           |
| Karpathy LLM-wiki module                     | `knowledge.md`                      |
| Vault without the terminal                   | `non-tech.md`                       |
| Finance / Legal / Security / Data / Support  | `non-engineering.md`                |
| Git commit and branch rules                  | `conventions/GIT_CONVENTION.md`     |
| Russian versions                             | `README.ru.md`, `i18n/ru/`          |

The framework itself prescribes where adopting teams put their own artefacts (`decisions/`, `patterns/`, `journal/`, `knowledge/`, `guides/`) and routes features to GitHub Issues rather than to a directory; see `framework.md` for that map. None of those directories exist in this repo, and a contributor working *on* the framework should not create them here.

A reader new to the framework should start with `README.md`, then either `framework.md` (for the full mental model) or `lite.md` (for the smaller version). A reader new to *this repo* — for example, an AI agent assisting with editing — should read this file plus `conventions/GIT_CONVENTION.md` before making any commits.

A reader trying to find the *rationale* for a layout choice should look for it here under "Invariants"; if it is not there, the rationale lives either in a commit message or in `framework.md`. There is intentionally no `RATIONALE.md` or `DESIGN.md` — this file is the only meta-document about the layout, and it stays small enough to read in one sitting.

## Non-goals

- **No CLI or tooling.** The framework is markdown plus conventions plus GitHub. Any future tooling — linting feature pages, validating ADR numbering, scaffolding directories from a template — belongs in a separate project, not in this repo. The framework's value is the conventions; tooling that automates them can be built on top, but living without it must remain possible.
- **No team-vault hosting.** This repo is not a SaaS or a template registry. Teams fork or copy the structure into their own private repository. There is no intent to add a "Use this template" workflow that pulls from this repo at runtime.
- **Stack-agnostic.** The framework does not dictate languages, build systems, or test frameworks. It expects each adopting team to record their stack in their own `STACK.md`, but it does not care what is in there. A Rails team and a Rust team should be able to use the same framework without changes.
- **Not a knowledge graph or wiki engine.** Cross-references are plain markdown links. There is no frontmatter beyond what GitHub renders, no custom build step, and no plugin model. The intended viewer is GitHub's rendered markdown plus a contributor's editor of choice.
- **Not opinionated about agents beyond `AGENTS.md`.** The framework names AGENTS.md as the place where AI-agent instructions live, and otherwise stays out of the way. There is no recommendation about which agent to use, which model to prefer, or how aggressively to delegate. The Karpathy module is the one exception, and it is opt-in via `knowledge.md`.
