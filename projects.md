# Shubin Framework: GitHub Projects v2 Integration

**Part of:** Shubin Framework (companion document)
**Who it's for:** the team's tech lead and PM, plus anyone configuring shared planning tooling.

**What it is:** a concrete spec for how a small product team uses GitHub Projects v2 as the cross-repo planning surface that ties together docs-repo cross-cutting Issues, code-repo sub-issues, sprints, and roadmap.

**What it isn't:** a tutorial of every Projects feature, a Linear/Jira replacement guide, or an opinion on agile methodology. The framework only cares about the parts of Projects that pay off given Issues-as-spec (`framework.md` chapter 4) and cross-cutting flows (chapter 4.3).

**Related documents:**
- `framework.md` — main framework, chapters 4 and 12
- `lite.md` — solo / 1–2-person setup, where Projects is unnecessary overhead
- `knowledge.md` — wiki module that reads Project items as one of its inputs

---

## 1. When you need a Project (and when you don't)

A GitHub Project is **required** when at least one of the following is true:

- The team works on a product that has two or more affected parts (typically frontend + backend, plus optional mobile/infra). Cross-cutting features happen routinely, so cross-repo aggregation is no longer optional.
- The team is roughly five or more people across roles. Tracking who is on what stops fitting into a Slack channel.
- Cross-repo aggregation is needed for planning, roadmap, or stakeholder reporting — even at a smaller team size.

A Project is **not** needed when:

- A solo developer or a 1–2 person team is building an MVP. The repository's open Issues list is enough.
- There is one code repo plus one docs repo, single developer. Add a Project later if the team grows.

If you are unsure, start without a Project. Adding one later costs an hour. Removing one because nobody used it adds friction — people get attached to fields they never look at.

---

## 2. One Project per product

The default is a single **organisation-level Project per product**, aggregating Issues from the docs repo and all code repos belonging to that product.

Why org-level rather than user-level: a Project owned by a personal account cannot pull cleanly from organisation-owned repositories. Make the Project under the same org as the repos.

Why one per product, not many small Projects: cross-cutting features need cross-repo visibility in one place. Splitting "frontend Project" and "backend Project" recreates exactly the silos the framework tries to avoid (`framework.md` chapter 13.8). Multiple Projects only when the org has genuinely separate products with their own teams and roadmaps.

For multi-product orgs, each product gets its own Project, and the Project Template (chapter 8) keeps the schema consistent.

---

## 3. Recommended structure

A working configuration of fields, views, and the iteration field for a 5–10 person team.

### 3.1. Custom fields

**Required.**

- **Status** (single-select): `Backlog`, `Ready`, `In Progress`, `In Review`, `Blocked`, `Done`. Six values is the upper bound — more is friction. Don't add `Cancelled` as a status; close the Issue with a `wontfix` label.
- **Priority** (single-select): `P0`, `P1`, `P2`. Stop there. P3 in practice means "not doing".
- **Affected parts** (multi-select): `frontend`, `backend`, `mobile`, `design`, `infra`. Cross-cutting Issues get multiple values; single-part Issues get one. This field is what makes the Project a real cross-repo surface — it drives filters, reporting, and (later) the knowledge module's domain assignment.
- **Iteration** (iteration field): see 3.3.

**Recommended.**

- **Effort** (single-select: `XS`, `S`, `M`, `L`, `XL`) for sprint-level capacity planning. Numeric story points are also fine, but XS-XL avoids the false precision of "5 vs 8".
- **Team** (single-select) when several teams share one Project. With one team, skip.
- **Type** (single-select: `Feature`, `Bug`, `Tech debt`, `Spike`, `Doc`). Useful for roadmap reporting and for the knowledge module — feature work and tech debt should be visible separately on the wiki page.

**Don't add.** "Owner" — use the native assignee. "Created" / "Updated" — built in. Anything that duplicates a native field. Anything you wouldn't filter by within a month — extra fields you don't filter by become decoration.

A note on history: as of early 2026, **custom field changes are not mirrored to the Issue timeline**. If audit history matters for a field, work around it with a GitHub Action listening to the `projects_v2_item.edited` webhook and posting an Issue comment. Most teams don't need this.

### 3.2. Views

A pragmatic starter set:

- **Backlog (Table)** — filter `Status: Backlog OR Ready`, group by Priority. The view PMs and tech leads keep open during weekly planning.
- **Current sprint (Board)** — filter `iteration:@current`, columns by Status. The view engineers keep open during the day.
- **Roadmap (Roadmap layout)** — date or iteration on the horizontal axis, group by Team or Iteration. For monthly and quarterly review, not daily.
- **Cross-cutting (Table or Board, hierarchy view enabled)** — filter `has:sub-issues`, hierarchy view on. The view that justifies the Project's existence: one main Issue with sub-issues across repos, all aggregated.
- **Per-team views** — duplicates of the sprint view, filtered by Team. Only when there are several teams.

Five views is the working maximum. More views is a signal someone is using the Project as a personal Notion.

### 3.3. Iteration field

One iteration field — `Iteration` or `Sprint` — with a 1- or 2-week cadence (2-week is the more common default in 5–10 person teams). GitHub auto-rolls iterations forward, so once configured the field requires no maintenance.

Filter syntax: `iteration:@current`, `iteration:@next`, `iteration:@previous`, ranges like `iteration:@current-2..@current+1` for retrospective views.

Don't run Milestones **and** Iterations for the same purpose. Iteration field for sprint-level work; GitHub Milestones for releases or quarter-level groupings. Mixing them invites the question "is the source of truth the milestone or the iteration?" — and the answer drifts.

---

## 4. Auto-add Issues across repos

The single biggest gap in native Projects v2: **auto-add is configured per-repo**. There is no built-in "add every Issue from every repo in this org tagged X" rule. Three workable options.

**Option A — GitHub Actions schedule** (recommended for ≤10 linked repos). A scheduled workflow in the docs repo runs every hour or every day, queries Issues across the listed code repos, and adds new ones to the Project via GraphQL. A short reference workflow lives at `guides/projects-auto-add-action.md` (created when the team first sets one up).

**Option B — per-repo native auto-add.** Configure auto-add separately in each linked repo. Pros: no custom code. Cons: must remember to configure it for every new repo, and the rules are limited to "add when label X is applied" or "add when an Issue is opened".

**Option C — third-party tool** (e.g. `ghproj` from szksh-lab). Use only when A and B both fail to fit, and accept the operational cost of an extra dependency.

The list of linked repositories — the source of truth for the auto-add target — lives in `knowledge/wiki/_meta.md` if the knowledge module is in use, otherwise in a short `projects-auto-add.md` runbook. One place. If the list is duplicated across CI configs and documentation, it will diverge.

---

## 5. Hierarchy view: cross-cutting features in action

The hierarchy view is the killer feature for the cross-cutting flow described in `framework.md` chapter 4.3. GA since March 2026.

Workflow for a cross-cutting feature:

1. In the docs repo, create the **main Issue** using the "Cross-cutting feature" template, plus native sub-issues for parts that live in the same repo (rare, mostly docs deliverables).
2. For sub-issues in **other repos**, create them with the Sub-issue template and reference the parent main Issue. Cross-repo native sub-issue links work — the parent shows them — but progress roll-up may be partial; treat the markdown checkbox list in the parent Issue as the authoritative completion checklist.
3. Add the main Issue and all sub-issues to the Project (auto-add covers this if Option A or B is configured).
4. In the relevant Project view, **View → Show hierarchy**. The main Issue now displays with its sub-issues nested underneath, regardless of which repo they live in.
5. Sort, group, and filter while preserving the hierarchy. Useful filters: `Status:!=Done`, `iteration:@current`, `assignee:@me`.

Limitations as of May 2026:

- Hierarchy view is fully supported in Board and Table layouts; **Roadmap layout doesn't yet show sub-issue nesting**. Use Board or Table for cross-cutting tracking.
- A sub-issue not added to the Project shows up as a "+ Add" affordance on the parent — it does not auto-add. Either configure auto-add or click + Add manually.

---

## 6. Issue dependencies

GA since August 2025; the REST API followed in March 2026. Each Issue can have up to 50 dependencies in either direction (`blocked-by` / `blocking`).

When to use:

- A backend Issue must merge before frontend can integrate. The frontend Issue is `blocked by` the backend Issue.
- A migration must run before a feature deploys. The feature is `blocked by` the migration.
- A spike must finish before a planning decision. The decision Issue is `blocked by` the spike.

When **not** to use:

- For sub-issue / parent relationships — use sub-issues, not dependencies. They are different concepts: sub-issues describe decomposition, dependencies describe ordering.
- For "this is similar to that" — use comments or labels.
- For weak coupling that won't actually block work. The list of dependencies should be short enough to act on.

Dependencies surface in the Issue sidebar, in the timeline as events, and as a "Blocked" badge on Project boards. Filter: `is:open is:blocked` to find what to unblock first.

API: REST endpoints under `GET/POST/DELETE /repos/{owner}/{repo}/issues/{n}/dependencies/blocked_by` and `.../blocking`. GraphQL exposes the same on `Issue.trackedInIssues` and related connections.

---

## 7. Roadmap layout

Configure: View → Layout → Roadmap. Set the date fields (Start, Target) or the Iteration field as the horizontal axis. Enable Iteration and Milestone markers.

Best practices:

- Roadmap is for **quarterly OKRs and release scheduling**, not daily tracking. The view is consulted weekly or monthly, not hourly.
- Group by Team or Iteration to spot capacity problems — three big rectangles overlapping in iteration N is a planning issue you can see at a glance.
- Don't track every Issue on the roadmap. Filter to features and epics (or by Type). The roadmap should fit on one screen for a 6-month window.

Limitation: Roadmap doesn't show sub-issue hierarchy as of May 2026. Cross-cutting features show up as a single bar; sub-issue progress is not nested. Use the hierarchy view (chapter 5) for that.

---

## 8. Templates: share configuration across teams and products

Configure one Project carefully — fields, views, iteration cadence — then mark it as a **Project template**. New products in the org use Create from template.

What templates copy: custom fields, views, iteration configuration, workflows (most), insights configuration.

What templates do **not** copy: items, auto-add workflows linked to specific repos, the linked repo list itself.

For an org running several products on the framework, the template is the mechanism that keeps schemas consistent. AI agents and humans alike rely on stable field names. If `Affected parts` is `Affected parts` in product A and `Areas` in product B, the knowledge module's logic and any cross-product reporting break. Use the template once, then leave it alone.

---

## 9. Cadence and rituals

The Project supports the rituals described in `framework.md` chapter 11; it does not invent new ones.

- **Daily (async).** Engineers update Status as they pick up and finish work. The Current sprint view is the surface — most engineers keep it open in a tab.
- **Weekly start.** Backlog review (the Backlog view), pick Issues for the new iteration, set Status to Ready and assign Iteration.
- **Weekly end.** Quick scan of what shipped versus what slipped. Slipped items either roll to next iteration with a comment, or get moved back to Backlog with priority adjusted.
- **Monthly.** Roadmap review (the Roadmap view). Adjust priorities, decompose anything for the next quarter that wasn't yet broken down.
- **Quarterly.** Insights charts and strategic adjustment. Output here is often a `journal/retros/` entry rather than a Project change.

The principle: the **Project is live state**, the **journal is historical chronicle**. Don't try to write Project history inside the Project; write it in `journal/retros/` and link out.

---

## 10. Limits and gotchas

The flat truth of where Projects v2 still hurts as of May 2026:

- **1200 active items** default per Project; archived items don't count. The increased limit (~50k) is in public preview from late 2025 — eligible for the org if larger.
- **Custom field changes are not in the Issue timeline.** Workaround via Actions webhook, see 3.1.
- **Cross-Project field sharing is not supported.** Each Project owns its own fields. The Project Template (chapter 8) is the only mechanism that propagates schema consistently.
- **Sub-issue hierarchy is per-repo natively; cross-repo aggregation only inside a Project.** A sub-issue in another repo is fully linked but won't show up in the parent's "Tasks" panel — it shows up via the Project's hierarchy view.
- **Roadmap doesn't show hierarchy.** Use Board / Table for hierarchy; Roadmap for time.
- **`gh` CLI for Projects v2** is functional but verbose. Useful third-party extensions: `gh-pm` (yahsan2), `gh-iteration`. A planned `gh-shubin` extension will wrap the Issue + Project flow into one command, but doesn't exist yet.

None of these are deal-breakers, and most have workarounds — but if the team encounters one of them mid-flight ("why isn't field history in the timeline?"), the answer should be a note in this document, not a discovery.

---

## 11. AI agents and Projects

The knowledge module's wiki maintainer agent (see `knowledge.md`) reads Project items as one of its inputs. The integration relies on three things being stable:

- The **Project ID and the linked repo list**, recorded in `knowledge/wiki/_meta.md`.
- The **field schema** — same field names across compile runs and across products in the org.
- The **Affected parts field**, which is the primary signal the agent uses to assign closed Issues to domain wiki pages. Without it, the agent falls back to label and title heuristics, which are noisier.

Agent operations via `gh`:

- Read items: `gh project item-list <number> --owner <org> --format json --limit 200`.
- Add an Issue: `gh project item-add <number> --owner <org> --url <issue-url>`.
- Update a single field on an item: GraphQL `updateProjectV2ItemFieldValue`. The CLI shortcut is `gh project item-edit`.

The agent **does not** modify the Project's structure — fields, views, workflows are human decisions. The agent only adds items it discovers (closed Issues from linked repos that aren't yet in the Project) and updates per-item field values when explicitly asked. Anything else is out of scope and should be flagged for human action.

---

## Final

A Project is a planning surface, not a knowledge store. It is **mutable state** in the framework's two-principles sense (`framework.md` 1.3): valuable while live, ephemeral as a record. The historical record stays in closed Issues, merged PRs, ADRs, and `journal/`.

Set the Project up once, keep the field set narrow, leave the views minimal, and resist the urge to make it the team's Notion. The Project pays off when it tracks ten cross-repo sub-issues at a glance — it does not pay off when it carries thirty custom fields no one filters by.

If, after three months of use, the team isn't opening the Project at least weekly — that is a signal to remove it, not to add fields.
