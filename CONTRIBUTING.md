# Contributing

Thanks for the interest. This is documentation, not code: changes are markdown edits, commits are reviewed for editorial fit, and the bar for adding new top-level structure is high.

## How to propose a change

1. **Open an issue first** with the use case behind the change. "I tried X on a team of Y and the doc said Z, which led to W" beats "the doc should say A". Concrete situations are easier to evaluate than abstract suggestions.
2. **Discussion in the issue.** A small edit may not need it; a structural change always does. The maintainer will say which.
3. **PR after agreement.** Reference the issue. Keep PRs small — a single concern per PR is much easier to review than a sweep.

For typo fixes and obviously broken links, skip the issue and open the PR directly.

## What is welcome

- **Counterexamples.** "We tried this on a team of 6 and it didn't work because…" — these are gold. The framework is a scaffold of practices, not a truth, and reports of where it breaks make it sharper.
- **Real cases.** A short retro of how a chapter played out on a real team, what was kept, what was dropped, what was added.
- **Reports of practices that didn't work for your team.** The framework does not need more affirmation; it needs more honest negative signal.
- **Plain prose tightening** of existing chapters. Anywhere a paragraph sounds like filler, a PR is welcome.

## What needs discussion before a PR

- **New top-level directories.** The repository layout (`decisions/`, `features/`, `patterns/`, `journal/`, `knowledge/`) is a convention adopted by every team using the framework. Adding to it changes their conventions too.
- **New canonical UPPER_CASE files at root.** Same reason.
- **Renaming an existing chapter or document.** Inbound links across the docs and from external readers break silently.
- **Material expansions of the principles section.** The framework rests on two principles; if a third is being proposed, the bar is high.

For any of these, an issue is mandatory.

## Style

Prose, not bullet lists by default. Honest warnings over feel-good marketing copy. No emojis. English in the root documents; Russian translations live in `i18n/ru/`. The voice is direct — "this fails when…" rather than "this might not be optimal under certain circumstances".

`ARCHITECTURE.md` is the contributor-facing map of this repository. Read it before a non-trivial PR.

## Languages

All root documents are in English. Russian translations are mirrored in `i18n/ru/`. If you change a root document substantively and you are confident in your Russian, update `i18n/ru/` in the same PR. If you are not confident in the translation, leave a TODO note at the top of the corresponding `i18n/ru/` file and open a follow-up issue rather than ship a degraded translation.

## Licensing

The framework is MIT-licensed (see [LICENSE](./LICENSE)). By submitting a change, you agree it is contributed under the same license. There is no CLA.
