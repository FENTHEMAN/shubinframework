# Feature cycle: a worked example

A complete walkthrough of `framework.md` chapter 5 with a fictional "onboarding redesign" feature. Numbers are illustrative and not benchmarks.

The repository is `your-org/product`. Code lives in two repos: `app` (frontend) and `api` (backend). The team is four people: a PM (Alice), two developers (Bob and Carol), and a designer (Dan).

The whole point of this example is to show what each artifact looks like under the v2 split — markdown for spec, GitHub for state — and how the steps move between them.

---

## Step 1: Idea

Alice notices that 60% of users drop off on the second onboarding screen and the average time on screen 2 is 145 seconds. She raises it in Monday's weekly meeting; the team agrees to look into it.

That afternoon she does two things at once.

She opens **GitHub Issue #142**:

```
Title: Onboarding redesign

Body:
60% drop-off on screen 2 of onboarding. Hypothesis: too many fields.
Spec: features/onboarding-redesign/index.md

Assignee: @alice
Labels:   P1, product
Milestone: May sprint
```

She creates `features/onboarding-redesign/index.md`:

```markdown
# Onboarding redesign

**Tracking:** [#142](https://github.com/your-org/product/issues/142)

## Why

Current onboarding — 60% drop-off on the second screen. Hypothesis:
too many fields at once.

## Scope

TBD after discussion.
```

She opens a PR in the markdown repository with that file. The PR body refers to #142 but does not close it — the issue stays open until the work ships.

Notice what is *not* in the markdown: no `status: idea`, no `created: 2026-04-18`, no `## Log` section. The state of the work — that it is *idea*, not *spec*, not *in-progress* — is the state of issue #142 plus the labels on it. Markdown holds prose; GitHub holds state.

---

## Step 2: Spec

Tuesday morning, Alice and Bob spend 45 minutes filling in the spec. Dan joins for the UX angle. They update the same `index.md`:

```markdown
# Onboarding redesign

**Tracking:** [#142](https://github.com/your-org/product/issues/142)

## Why

Current onboarding — 60% drop-off on the second screen. Hypothesis:
too many fields at once.

## Scope

- 4 screens instead of 5
- Progress bar on top
- Option to skip onboarding
- Progress saving (resume if user leaves)

NOT doing in this iteration:

- Video tutorial
- Per-plan personalization
- A/B test of variants

## Success criteria

- Drop-off on screen 2 below 30% (now 60%)
- Median completion time below 90 seconds
- Measurement two weeks after release

## Decisions

(none yet)

## Design

(pending)
```

PR approved, merged. Alice swaps the issue's `idea` label for `validated` (or whatever the team uses). She does not write "validated" anywhere in the markdown.

---

## Step 3: ADR

Bob notices: progress saving — where do we store it? localStorage? DB? Cross-system decision, hard to reverse → ADR.

He writes `decisions/0023-onboarding-progress-storage.md`:

```markdown
# ADR-0023: Storing onboarding progress

## Status

Accepted — 2026-04-19

## Context

User can leave onboarding and return later. We need to save which step
they're on and what they've already filled in.

## Options considered

### 1. localStorage

- Simple, zero server logic.
- Lost on device change.

### 2. Field in users table

- Cross-device.
- Migration.

### 3. Separate onboarding_progress table

- Clean separation.
- Extra table for one feature.

## Decision

`onboarding_state JSONB` field in users table.

## Consequences

- Cross-device.
- Migration needed (see backend PR).

## Related

- features/onboarding-redesign/
- Tracking: #142
```

He opens a PR adding this file. Carol reviews, approves, merges. Bob updates the spec page's `## Decisions` section to:

```markdown
## Decisions

- ADR-0023: onboarding progress storage
```

The ADR does not get its own GitHub Issue. ADRs are decision records, not work in flight. Their `Status` field is part of the immutable document — the only state they have.

---

## Step 4: Design

Dan opens the Figma project, creates a new file `[Feature] Onboarding`, places four frames in the `In progress` section, builds a click-through prototype.

Bob updates the spec's `## Design` section:

```markdown
## Design

Figma: https://figma.com/file/.../Onboarding

- Section "In progress" — final screens
- Prototype: Play mode for demo transitions
```

Small PR, merged. Issue #142 does not change — design is part of the spec, not a separate state.

---

## Step 5: Implementation

Bob picks up the work. In the `app` repo:

```
git checkout -b feature/onboarding-redesign
```

He runs Claude Code:

```
> Implement the onboarding redesign. Context:
> - features/onboarding-redesign/index.md
> - decisions/0023-onboarding-progress-storage.md
> - Figma: <link from index.md>
>
> Use Figma MCP to read frames.
> Use components from shared/design-system.
> Check patterns/forms.md.
```

Claude reads the spec, the ADR, the patterns. It uses Figma MCP for frames and generates code. Bob reviews, fixes the loading and error states Claude missed, writes tests for form validation.

He opens **PR #87** in `app`:

```
Title: feat(onboarding): redesign onboarding flow

Body:
Closes your-org/product#142

Implements features/onboarding-redesign per ADR-0023.
```

Carol opens **PR #45** in `api` with the migration adding `onboarding_state`:

```
Title: feat(api): add onboarding_state column

Body:
Refs your-org/product#142
```

Bob does **not** edit the spec page to add an `## Implementation` section listing PR links. Issue #142's "Linked pull requests" panel shows them automatically. The spec stays stateless.

---

## Step 6: Preview + review

CI deploys the app PR to a preview environment. Dan opens the preview, compares with Figma, leaves three PR comments about spacing and a missing hover state. Alice walks through the flow, confirms scope is met. Bob fixes, pushes, the preview redeploys. Two iterations later Dan and Alice approve.

---

## Step 7: Merge + deploy

Carol approves the backend PR. The migration runs in production via the deploy pipeline. The app PR merges with `Closes your-org/product#142` — issue #142 auto-closes. Production picks up the change.

Bob does **not** edit `features/onboarding-redesign/index.md` to mark it shipped. There is no "shipped" marker in markdown. The state of the issue (closed) and the linked PRs (merged) is the source of truth. The spec page is now historical: it describes what was built, and it stays that way.

---

## Step 8: Feedback loop

Two weeks later Alice checks PostHog. She creates `journal/feedback/2026-05-12-onboarding-results.md`:

```markdown
# Onboarding redesign — results, two weeks in

Reference: features/onboarding-redesign/, issue #142.

## Numbers

- Drop-off on screen 2: 24% (target was below 30% — success).
- Median completion: 78 seconds (target was below 90 — success).
- Skip rate: 12%. We did not have a target here; noteworthy.

## User quotes

- "Took me half a minute, didn't even notice it was there." (interview, 2026-05-08)
- "I skipped because I just wanted to see the dashboard." (support ticket #214)

## Conclusions

The skip option is used more than expected. Worth a follow-up feature: an
onboarding teaser inside the dashboard for users who skipped.
```

Append-only. The file is dated; it never gets edited. If a follow-up feature emerges, it is a new feature with a new issue and a new spec page.

---

## What happened in each repo

In the markdown repository:

- 1 PR for the spec page draft (the title plus Why)
- 1 PR for scope and success criteria
- 1 PR for the ADR
- 1 PR for the Figma link and decisions reference
- 1 PR for the feedback file

Five small PRs, none of them touching the spec page after step 4. The spec is finalized at step 4 and stays as-is forever — or gets superseded by a future feature page.

In the `app` repo: 1 PR (#87), branch `feature/onboarding-redesign`, two iterations after preview review.

In the `api` repo: 1 PR (#45), branch `feature/onboarding-progress`, single migration.

---

## Compared to the v1 way

In v1 the spec page carried `status: idea` → `validated` → `in-progress` → `shipped` frontmatter, plus a `## Log` section with hand-typed dates ("2026-04-18: created, 2026-04-19: ADR accepted, ..."). Every status change was a small PR. Half of those PRs were never opened, because someone forgot, and feature pages drifted out of sync with reality within a couple of months.

In v2 the same information is in GitHub Issues — for free, automatically, with timestamps, with assignees, with the linked PRs panel. The spec page holds what GitHub cannot: the prose of *why* and *what*.

The trade-off: the team must use GitHub Issues seriously. If your team does not open issues for features, the v1 style is closer to your reality — and the framework probably is not a fit yet.

---

## When reality diverges from plan

Sometimes at step 5 the developer realizes the approach chosen in the ADR doesn't work — perhaps the JSONB field idea hits a constraint nobody anticipated.

Don't silently change the code and forget the ADR.

Do:

1. Update ADR-0023's `Status` to `Superseded by ADR-0024`.
2. Create ADR-0024 with the new decision and the reason for rejecting the old one.
3. Update `features/onboarding-redesign/index.md` if scope changed.
4. Note the change in issue #142 as a comment — not as a hand-typed log line in markdown.

Fifteen minutes. Six months later nobody scratches their head about why the code says one thing and the ADR says another.
