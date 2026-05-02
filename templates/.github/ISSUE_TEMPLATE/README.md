# Issue Templates for Shubin Framework

Copy these into the relevant project repos at `.github/ISSUE_TEMPLATE/`.

**For code repos** (frontend, backend, mobile, infra):

- `feature.md` — main feature template, mandatory for P0 and P1 single-part features.
- `feature-light.md` — for P2 features and small improvements.
- `bug.md` — bug reports.
- `sub-issue.md` — sub-issue belonging to a parent Issue (typically a docs-repo cross-cutting Issue).

**For the docs repo (vault):**

- `cross-cutting.md` — main Issue for features touching two or more parts.
- `vault-task.md` — docs-only work (writing a guide, fixing GLOSSARY, translation).
- `adr-initiative.md` — large ADR effort with multiple PRs.
- `retro-item.md` — follow-up action from a retrospective.

The shipped `config.yml` enables blank issues and points to Discussions plus the docs repo. Adjust the URLs to your org and product before committing.

For the framework rules on which template applies when (and why the docs repo gets cross-cutting Issues), see `framework.md` chapter 4 — in particular 4.2 *Where Issues live* and 4.3 *Cross-cutting features*.

## Future tooling

A planned `gh-shubin` CLI extension will create Issues from these templates with prefilled fields, link sub-issues across repos, and update Project fields in one command. Until then, use templates manually via the GitHub web UI or `gh issue create --template <name>`.
