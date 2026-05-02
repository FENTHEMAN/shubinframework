# Changelog

All notable changes to Shubin Framework are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/).

## [2.0.0] — 2026-05-02

Major rewrite. Breaking — no migration path provided (no users on v1).

### Added
- `ARCHITECTURE.md` — matklad-style code map for the repository.
- `CONTRIBUTING.md`.
- New chapter in `framework.md`: Scaling — single repo, hybrid `modules/`, triggers (2 of 3).
- Four new anti-patterns: vault per tech layer; premature module split; reinventing GitHub; feature page as Issue cache.
- `guides/` directory; running example for the feature cycle extracted there.

### Changed
- Repository layout flattened: `vault/stable/active/log/` dissolved into root + flat dirs.
- `vault/stable/*.md` → root with UPPER_CASE: `STACK.md`, `GLOSSARY.md`, `TEAM.md` (used as conventions in adopting projects).
- `vault/active/{decisions,patterns}/` → root `decisions/`, `patterns/`. `vault/active/features/` removed entirely — features are GitHub Issues.
- `vault/log/` → `journal/`; `retrospectives/` → `retros/`.
- `vault/knowledge/` → `knowledge/`.
- `vault/COMMON.md` → `AGENTS.md` — adopting the AGENTS.md cross-tool standard.
- `vault/knowledge/CLAUDE.md` → `knowledge/AGENTS.md`.
- `framework.md` slimmed from 1133 lines to roughly 700. Design Process / Infrastructure / Testing / Rituals chapters condensed; the long onboarding running example moved to `guides/feature-cycle-example.md`.
- **Features live in GitHub Issues, not in markdown.** No `features/` directory. The Issue body — structured by an Issue Template (Why / Scope / Success criteria / Decisions / Design) — is the spec, the tracker, and the conversation. ADRs, patterns, journal stay in markdown; everything about *workflow* (status, ownership, priority, milestones, comments, linked PRs) lives in the Issue. The closed Issue is the historical record of the feature.
- Feature iteration cycle compressed from eight steps to six: Idea→Issue, Discussion (optional), ADR (if needed), Implementation, Merge, Retrospective signal (if needed).
- `README.md` rewritten and absorbed `intro.md`'s navigation function.
- `i18n/ru/` resynced in full to v2 (framework, lite, knowledge, non-tech, non-engineering, plus `guides/feature-cycle-example.md`). `i18n/ru/framework.md` will be retranslated against the GitHub-Issue cycle as a follow-up before v2.0.0 ships.

### Removed
- `intro.md` (merged into `README.md`).
- `vault/` directory and the term as a distinct concept.
- `COMMON.md` (replaced by `AGENTS.md`).
- `features/` directory — including the v2-interim "stateless spec page" model. Features now live entirely as GitHub Issues; the Issue Template carries the structure that the markdown spec used to carry.
- `index.md` (the README is the index).
- `i18n/en/` (the repository root is now the English source; duplicating it under `i18n/en/` only created drift).

### Naming conventions
- UPPER_CASE in root = canonical document (rarely changes).
- lowercase in root = framework spec for a specific audience.
- flat dirs at root = collections by document type.

### Migration
None. v1 had no users; v2 is the new baseline.

## [1.0.0] — 2026-04-18

Initial public release.
