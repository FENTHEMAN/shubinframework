# Changelog

All notable changes to Shubin Framework are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/).

## [2.0.0] — 2026-05-02

Major rewrite. Breaking — no migration path provided (no users on v1).

### Added
- `ARCHITECTURE.md` — matklad-style code map for the repository.
- `CONTRIBUTING.md`.
- New chapter in `framework.md`: Scaling — single repo, hybrid `modules/`, triggers (2 of 3).
- Three new anti-patterns: vault per tech layer; premature module split; reinventing GitHub.
- `guides/` directory; running example for the feature cycle extracted there.

### Changed
- Repository layout flattened: `vault/stable/active/log/` dissolved into root + flat dirs.
- `vault/stable/*.md` → root with UPPER_CASE: `STACK.md`, `GLOSSARY.md`, `TEAM.md` (used as conventions in adopting projects).
- `vault/active/{decisions,features,patterns}/` → root `decisions/`, `features/`, `patterns/`.
- `vault/log/` → `journal/`; `retrospectives/` → `retros/`.
- `vault/knowledge/` → `knowledge/`.
- `vault/COMMON.md` → `AGENTS.md` — adopting the AGENTS.md cross-tool standard.
- `vault/knowledge/CLAUDE.md` → `knowledge/AGENTS.md`.
- `framework.md` slimmed from 1133 → 692 lines. Design Process / Infrastructure / Testing / Rituals chapters condensed; the long onboarding running example moved to `guides/feature-cycle-example.md`.
- **Feature pages are now stateless specifications.** Status, ownership, priority, dates, and progress live in GitHub Issues + Milestones + linked PRs. Markdown carries knowledge; GitHub carries state.
- `README.md` rewritten and absorbed `intro.md`'s navigation function.
- `i18n/ru/` resynced in full to v2 (framework, lite, knowledge, non-tech, non-engineering, plus `guides/feature-cycle-example.md`).

### Removed
- `intro.md` (merged into `README.md`).
- `vault/` directory and the term as a distinct concept.
- `COMMON.md` (replaced by `AGENTS.md`).
- Stateful frontmatter on feature pages (`status` / `owner` / `priority` / `created` / `target` and the `## Log` section). All of this state now lives in GitHub Issues.
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
