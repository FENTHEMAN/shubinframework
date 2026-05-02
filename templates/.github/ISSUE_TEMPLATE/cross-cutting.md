---
name: Cross-cutting feature
about: Feature touching two or more parts (frontend + backend, etc.)
title: ''
labels: ['cross-cutting', 'feature']
assignees: ''
---

<!--
This Issue lives in the docs repo as the main spec for a feature
spanning multiple code repos or parts. Sub-issues open in each
respective code or design repo, using the Sub-issue template.
See framework.md ch. 4.3.
-->

## Why

<!-- One paragraph: who is affected, what problem this solves, why now. -->

## Scope

In:
-

Out:
-

## Affected parts

<!-- Tick each part that needs work. Each ticked part needs a sub-issue. -->

- [ ] Frontend (`acme-frontend`)
- [ ] Backend (`acme-backend`)
- [ ] Mobile (`acme-mobile`)
- [ ] Design (sub-issue in design repo)
- [ ] Infra (`acme-infra`)
- [ ] Other:

## Sub-issues

<!-- Filled as sub-issues open. Format: - [ ] repo#NNN — title. Tick on close. -->

- [ ]

## Success criteria

<!-- User-visible measurable checks. Not per-part. -->

-

## Decisions

<!-- ADRs in this docs repo. -->

-

## Design

<!-- Figma link, if applicable. -->

## Closing

This Issue closes manually when:

- All sub-issues above are closed.
- All ADRs listed are merged.
- Required updates to ARCHITECTURE.md, GLOSSARY.md, etc. are merged.
