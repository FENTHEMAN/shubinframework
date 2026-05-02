# Changelog

All notable changes to Shubin Framework are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

Major rewrite. Breaking — no migration path provided (no users on v1). The version becomes `2.0.0` once tagged in the release stage.

### Added
- `ARCHITECTURE.md` — matklad-style code map for the repository.
- `CONTRIBUTING.md`.
- `AGENTS.md` at the repository root — rules for AI agents working *on* this repository (which documents to read first, the invariants to honour, the ADR format and prose style).
- `projects.md` — eleven-section integration spec for GitHub Projects v2: when a Project is required, recommended custom fields and views, the iteration field, cross-repo auto-add, hierarchy view for cross-cutting features, Issue dependencies, roadmap, project templates, cadence, known limits, and AI agent operations.
- `templates/` directory holding files an adopting team copies into their repos, with a `templates/README.md` explaining the convention. Contents:
  - `templates/.github/ISSUE_TEMPLATE/feature.md` — mandatory structure for P0 and P1 features (Why / Scope / Success criteria / Decisions / Design).
  - `templates/.github/ISSUE_TEMPLATE/feature-light.md` — small or P2 features.
  - `templates/.github/ISSUE_TEMPLATE/bug.md` — light bug template.
  - `templates/.github/ISSUE_TEMPLATE/sub-issue.md` — implementation slice referencing a parent Issue.
  - `templates/.github/ISSUE_TEMPLATE/cross-cutting.md` — main Issue for features touching two or more parts (lives in the docs repo).
  - `templates/.github/ISSUE_TEMPLATE/vault-task.md` — docs-only work.
  - `templates/.github/ISSUE_TEMPLATE/adr-initiative.md` — large ADR effort with multiple PRs.
  - `templates/.github/ISSUE_TEMPLATE/retro-item.md` — follow-up action from a retrospective.
  - `templates/.github/ISSUE_TEMPLATE/config.yml` and `templates/.github/ISSUE_TEMPLATE/README.md` (split by code-repo vs docs-repo target).
  - `templates/knowledge/AGENTS.md` — auto-ingest spec for the optional knowledge module: inputs (raw, ADRs, journal, closed Issues, merged PRs, Project items), outputs (one wiki page per product domain plus an optional second discovery orientation), page format, weekly compile cadence, Project integration mechanics, and the explicit non-goals.
- New chapters in `framework.md`:
  - 3 — Scaling: single repo, hybrid `modules/`, triggers (2 of 3).
  - 4.2 — Where Issues live (single rule for routing single-part vs cross-cutting).
  - 4.3 — Cross-cutting features (main Issue in docs repo, sub-issues across code repos).
  - 12 — GitHub Projects v2 overview (short; full spec in `projects.md`).
- Four new anti-patterns: vault per tech layer; premature module split; reinventing GitHub; feature page as Issue cache.
- New chapters in `knowledge.md` (10–15) covering: dual orientation (product-discovery vs engineering-domain wiki pages), broader auto-ingest inputs (ADRs, journal, Issues, PRs, Project items), Project integration via the `Affected parts` field, deployment options (Action / manual / hybrid), end-to-end fictional team walkthrough, and migration from no-module.
- `guides/` directory; running example for the feature cycle extracted there.

### Changed
- Repository layout flattened: `vault/stable/active/log/` dissolved into root + flat dirs.
- `vault/stable/*.md` → root with UPPER_CASE: `STACK.md`, `GLOSSARY.md`, `TEAM.md` (used as conventions in adopting projects).
- `vault/active/{decisions,patterns}/` → root `decisions/`, `patterns/`. `vault/active/features/` removed entirely — features are GitHub Issues.
- `vault/log/` → `journal/`; `retrospectives/` → `retros/`.
- `vault/knowledge/` → `knowledge/`.
- `vault/COMMON.md` → `AGENTS.md` — adopting the AGENTS.md cross-tool standard.
- `vault/knowledge/CLAUDE.md` → `knowledge/AGENTS.md` for adopting teams; the canonical example shipped with the framework lives at `templates/knowledge/AGENTS.md`.
- `framework.md` slimmed from 1133 lines to ~760. Design Process / Infrastructure / Testing / Rituals chapters condensed; the long onboarding running example moved to `guides/feature-cycle-example.md` and rewritten around the Issue-first cycle. Anti-patterns chapter renumbered to 13 and Phased rollout to 14 to make room for the new Projects v2 chapter.
- **Features live in GitHub Issues, not in markdown.** No `features/` directory. The Issue body — structured by an Issue Template (Why / Scope / Success criteria / Decisions / Design) — is the spec, the tracker, and the conversation. ADRs, patterns, journal stay in markdown; everything about *workflow* (status, ownership, priority, milestones, comments, linked PRs) lives in the Issue. The closed Issue is the historical record of the feature.
- Feature iteration cycle compressed from eight steps to six: Idea→Issue, Discussion (optional), ADR (if needed), Implementation, Merge, Retrospective signal (if needed).
- `README.md` rewritten and absorbed `intro.md`'s navigation function; introduces the three coordinated layers up front (markdown + GitHub Issues/Projects + AI compile) and lists `projects.md` in the layout block and reading router.
- `lite.md` — graduation triggers expanded from three to five (second product part, first enterprise customer, knowledge no longer fits in one head); Projects-not-needed note added to the anti-patterns list.
- `non-tech.md` — added new chapter 7 *Projects v2 in the web UI* for non-engineers using the team's Project board (views, drag-drop Status, custom fields, hierarchy view for cross-cutting features, what not to change). Subsequent chapters renumbered 7 → 8 ... 11 → 12; a pre-existing 6.1 anomaly fixed to 8.1.
- `i18n/ru/` resynced in full to v2 (framework, lite, knowledge, non-tech, non-engineering, plus `guides/feature-cycle-example.md`). `i18n/ru/framework.md` and `i18n/ru/guides/feature-cycle-example.md` retranslated against the Issue-first feature cycle. Stage 1 and 2 additions mirrored where applicable; see `i18n/ru/SYNC_STATUS.md` for the per-file decisions.
- `i18n/ru/templates/` added — Russian mirror of `templates/` (Issue Templates and `knowledge/AGENTS.md`) for Russian-speaking teams to copy from.

### Removed
- `intro.md` (merged into `README.md`).
- `vault/` directory and the term as a distinct concept.
- `COMMON.md` (replaced by `AGENTS.md`).
- `features/` directory — including the v2-interim "stateless spec page" model. Features now live entirely as GitHub Issues; the Issue Template carries the structure that the markdown spec used to carry.
- `index.md` (the README is the index).
- `i18n/en/` (the repository root is now the English source; duplicating it under `i18n/en/` only created drift).
- Per-document `**Version:**` field. The framework version lives in the git tag and this CHANGELOG; per-file Version lines drifted and were redundant with both.

### Naming conventions
- UPPER_CASE in root = canonical document (rarely changes).
- lowercase in root = framework spec for a specific audience.
- flat dirs at root = collections by document type.

### Migration
None. v1 had no users; v2 is the new baseline.

## [1.0.0] — 2026-04-18

Initial public release.
