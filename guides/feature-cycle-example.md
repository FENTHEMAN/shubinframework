# Feature cycle: a worked example

A complete walkthrough of `framework.md` chapter 5 with a fictional "onboarding redesign" feature. Numbers are illustrative and not benchmarks.

The repository is `your-org/product`. Code lives in two repos: `app` (frontend) and `api` (backend). The team is four people: a PM (Alice), two developers (Bob and Carol), and a designer (Dan).

The whole point of this example is to show what each artifact looks like under the v2 split — GitHub Issues for the live workflow, markdown for the durable knowledge — and how the steps move between them.

---

## Step 1: Idea → Issue

Alice notices that 60% of users drop off on the second onboarding screen and the average time on screen 2 is 145 seconds. She raises it in Monday's weekly meeting; the team agrees to look into it.

That afternoon she opens **GitHub Issue #142** in `your-org/product` using the Feature Template:

```
Title: Onboarding redesign

## Why
Current onboarding has a 60% drop-off on screen 2; average time on
that screen is 145 seconds. Hypothesis: too many fields at once.

## Scope
TBD after discussion.

## Success criteria
TBD after discussion.

## Decisions
(none yet)

## Design
(pending)

Assignee: @alice
Labels:   P1, product
Milestone: May sprint
```

That's it. No markdown PR. No `features/onboarding-redesign/index.md`. The Issue is the spec; the labels and milestone carry the state.

---

## Step 2: Discussion

Tuesday morning Alice and Bob spend 45 minutes filling in scope and success criteria as a series of comments and edits to the Issue body. Dan joins for the UX angle. The Issue body becomes:

```
## Why
Current onboarding has a 60% drop-off on screen 2; average time on
that screen is 145 seconds. Hypothesis: too many fields at once.

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

Alice swaps the `idea` label for `validated` (or whatever the team uses). She does not write "validated" anywhere else — labels are the state.

The Issue's "Edited by Alice" history shows when the body changed. The discussion comments stay where they belong — under the Issue, not duplicated into a markdown log.

---

## Step 3: ADR

Bob notices a real choice: progress saving — where do we store it? localStorage, a column in `users`, or a separate table? Cross-system, hard to reverse → ADR.

He opens a PR in the markdown repository adding `decisions/0023-onboarding-progress-storage.md`:

```markdown
# ADR-0023: Storing onboarding progress

## Status

Accepted — 2026-04-19

## Context

Users can leave onboarding and return later. We need to save which
step they're on and what they've already filled in.

## Options considered

### 1. localStorage
- Simple, zero server logic.
- Lost on device change.

### 2. Field in users table
- Cross-device.
- Migration needed.

### 3. Separate onboarding_progress table
- Clean separation.
- Extra table for one feature.

## Decision

`onboarding_state JSONB` field in users table.

## Consequences

- Cross-device.
- Migration needed (see backend PR).

## Related

- Issue #142.
```

Carol reviews, approves, merges.

Bob then edits the Issue body's `## Decisions` section to:

```
## Decisions
- ADR-0023: onboarding progress storage
  (your-org/markdown-repo/decisions/0023-onboarding-progress-storage.md)
```

The ADR does not get its own GitHub Issue. ADRs are decision records, not work in flight; their `Status` field is the only state they have. The link is one-directional: the Issue points to the ADR; the ADR optionally mentions the Issue under `## Related`, but doesn't have to.

Design happens in parallel: Dan opens the Figma project, builds a click-through prototype for the four new screens, and pastes the Figma link into the Issue's `## Design` section. No PR for this — it's an Issue body edit.

---

## Step 4: Implementation

Bob picks up the work. In the `app` repo:

```
git checkout -b feature/onboarding-redesign
```

He runs Claude Code:

```
> Implement the onboarding redesign per Issue your-org/product#142.
> Read the Issue body for Why / Scope / Success criteria.
> ADR: decisions/0023-onboarding-progress-storage.md.
> Figma: <link from the Issue's Design section>.
>
> Use Figma MCP to read frames.
> Use components from shared/design-system.
> Check patterns/forms.md.
```

Claude reads the Issue, the ADR, the patterns. It uses Figma MCP for frames and generates code. Bob reviews, fixes the loading and error states Claude missed, writes tests for form validation.

He opens **PR #87** in `app`:

```
Title: feat(onboarding): redesign onboarding flow

Body:
Closes your-org/product#142

Implements onboarding redesign per ADR-0023.
```

Carol opens **PR #45** in `api` with the migration adding `onboarding_state`:

```
Title: feat(api): add onboarding_state column

Body:
Refs your-org/product#142
```

Both PRs appear automatically in Issue #142's Linked Pull Requests panel — no manual cross-linking, no markdown edit to record them.

CI deploys the app PR to a preview environment. Dan opens the preview, compares with Figma, leaves three PR comments about spacing and a missing hover state. Alice walks through the flow on the preview, confirms scope is met. Bob fixes, pushes, the preview redeploys. Two iterations later Dan and Alice approve.

---

## Step 5: Merge

Carol approves the backend PR. The migration runs in production via the deploy pipeline. The app PR merges with `Closes your-org/product#142` — Issue #142 auto-closes. Production picks up the change.

Nothing is edited in markdown to mark the feature shipped. There is no "shipped" marker — the closed Issue (with its original Why/Scope/Success criteria, the discussion, and the linked merged PRs) is the historical record.

---

## Step 6: Retrospective signal

Two weeks later Alice checks PostHog. The numbers are good and there's a quiet surprise worth recording. She opens a PR adding `journal/feedback/2026-05-12-onboarding-results.md`:

```markdown
# Onboarding redesign — results, two weeks in

Reference: your-org/product#142, ADR-0023.

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

Append-only. The file is dated; it never gets edited. If a follow-up feature emerges, it's a new Issue.

If the feature had shipped smoothly with no surprises, this step would have been skipped — the closed Issue would be enough. Don't write retros for the sake of writing retros.

---

## What happened in each repo

In the markdown repository:

- 1 PR for the ADR (`decisions/0023-onboarding-progress-storage.md`).
- 1 PR for the feedback file (`journal/feedback/2026-05-12-onboarding-results.md`).

Two small PRs, both touching files that exist exactly once and are never re-edited.

In `your-org/product` (Issues): 1 Issue (#142) — opened, discussed, edited a few times to fill in scope/decisions/design, closed automatically by the merging PR.

In the `app` repo: 1 PR (#87), branch `feature/onboarding-redesign`, two iterations after preview review.

In the `api` repo: 1 PR (#45), branch `feature/onboarding-progress`, single migration.

Compare to the v1 way: a `features/onboarding-redesign/index.md` page with `status: idea → validated → in-progress → shipped` frontmatter and a hand-typed `## Log` section. Every status change was a small PR — half of those PRs were never opened, because someone forgot, and feature pages drifted out of sync with reality within a couple of months. v2 puts that information in the Issue: for free, automatically, with timestamps, assignees, and the linked PRs panel.

The trade-off: the team must use GitHub Issues seriously. If your team doesn't open Issues for features, v2 isn't a fit yet — close that gap first.

---

## When reality diverges from plan

Sometimes at step 4 the developer realises the approach chosen in the ADR doesn't work — the JSONB field idea hits a constraint nobody anticipated.

Don't silently change the code and forget the ADR.

Do:

1. Update ADR-0023's `Status` to `Superseded by ADR-0024`.
2. Open ADR-0024 with the new decision and the reason for rejecting the old one.
3. Edit Issue #142's body if scope changed; link the new ADR from `## Decisions`.
4. Leave a comment on Issue #142 summarising the pivot.

Fifteen minutes. Six months later nobody scratches their head about why the code says one thing and the ADR says another.
