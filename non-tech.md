# Shubin Framework: the repository for non-technical team members

**Full title:** Guide to working with the repository and git without the terminal
**Part of:** Shubin Framework (companion document)

**Who it's for:** PM, designer, marketer, founder, domain expert — any team member who has to work with the team's markdown repository but has never opened a terminal.

**Reading time:** 30 minutes.
**Initial setup time:** 45 minutes.

**What you do NOT need to know before reading:**
- Programming
- The command line
- What git is, before today

**What you need to know about context:**
Your team uses a shared markdown repository where the team's knowledge lives — architecture, decisions, glossary, journals, research. It's synchronized via git, a system that keeps the history of all changes and lets you work together without conflicts.

You make changes to that repository — add a glossary term, log an interview, drop a research source, capture feedback. Features themselves do not live in this repository in v2: they are GitHub Issues, opened in the browser. This document teaches you how to handle the repository part without pain; the Issue side is covered in section 6.

**Related documents:**
- `framework.md` — full framework for the team's tech lead. Optional reading.
- `knowledge.md` — LLM wiki module. If your team adopted it — you'll need section 9 here.

---

## 1. What is git and why you need it

Very briefly:

**Git** is a system that keeps the history of changes to your files. Each change is a "commit" with author, date, and description. You can roll back to any previous version. You can work simultaneously with other people without conflicts.

**GitHub / GitLab / Bitbucket** are websites that host a copy of your git repository, accessible to everyone on the team.

**Repository (repo)** is a project folder tracked by git. Your team probably has several: the markdown repository (where knowledge lives), frontend, backend, etc.

To work with the markdown repository, you need to understand four operations:
- **clone** — download the repository from GitHub to your computer (once at the start)
- **pull** — update your local copy from GitHub (get other people's changes)
- **commit** — save your changes locally with a description
- **push** — send your commits to GitHub (so others can see them)

These four operations cover 95% of the work.

---

## 2. Tool: GitHub Desktop

Developers work with git from the command line. You don't need that — use a GUI client.

**Recommended tools:**

**GitHub Desktop** ([desktop.github.com](https://desktop.github.com)) — free, simple interface, perfect to start. Works with GitHub out of the box. All examples in this document assume it.

**Obsidian with git plugin** — if you already edit the repository in Obsidian, the "Obsidian Git" plugin automates pull/commit/push in the background. Downside: less control, occasionally commits junk. Works for those who want to forget about git entirely.

**Start with GitHub Desktop.** If in a month you want something else — you'll try it.

---

## 3. Initial setup (one-time, 15 minutes)

### 3.1. What to ask the team on day one

Before starting, ask the developer or tech lead for:
- The repository URL on GitHub
- Access to the GitHub organization (you must be added as a member)
- The name you'll be added under in `TEAM.md`
- The email you'll use for commits (usually your work email)
- A 10-minute tour of the structure: where what lives, what you can edit, what to leave alone

This is standard onboarding — the developer has done it for others. Don't hesitate to ask.

### 3.2. Setup steps

**Step 1.** Install GitHub Desktop from [desktop.github.com](https://desktop.github.com).

**Step 2.** Launch it. Sign in to your GitHub account. If you don't have one — register first, then ask to be added to the organization.

**Step 3.** Install a markdown editor:
- **Obsidian** ([obsidian.md](https://obsidian.md)) — free, purpose-built for vaults, link graph, convenient search. Default recommendation.
- **VS Code** ([code.visualstudio.com](https://code.visualstudio.com)) — if you're curious about looking at team code.
- Any markdown editor from your OS app store — if you don't want to install anything new.

**Step 4.** In GitHub Desktop: File → Clone Repository → URL. Paste the repository URL. Pick a folder on your computer, e.g., `~/work/<repo-name>/`. Click Clone.

In 30 seconds, a copy of the repository appears on your computer. Open it in Obsidian (or another editor) as a vault — that's Obsidian's term for an opened folder, unrelated to the framework.

**Done.** You now have a working copy of the team's knowledge on your computer.

---

## 4. Daily work cycle

Every time you sit down to work with the repository, repeat the same four-step sequence.

**Step 1: Pull (get fresh stuff, 5 seconds).**

Open GitHub Desktop. If you see a "Pull origin" button with a commit count — click it. This downloads changes others made.

Do this **unconditionally before starting work**. Skip it and you risk creating a merge conflict.

**Step 2: Work in your editor.**

Open the needed files in Obsidian, edit, create new ones. No git interaction here — git automatically notices all changes.

**Step 3: Commit (save changes, 30 seconds).**

When you've done something meaningful (finished a thought, added a decision, updated a status) — switch to GitHub Desktop. On the left you'll see a list of modified files.

In the bottom-left corner — "Summary" (short description) and "Description" (optional, longer) fields.

Good summary examples:
- `add ADR-0024: switch from REST to tRPC`
- `glossary: add 'product-led growth' term`
- `add new pattern: error handling on server`
- `feedback: 3 user interviews summary`

Bad examples:
- `update`
- `changes`
- `file update`

After filling in — click "Commit to main".

**Step 4: Push (send to GitHub, 5 seconds).**

After commit, a "Push origin" button appears at the top. Click it — your changes fly off to GitHub, everyone else can pick them up via their Pull.

**Cycle complete.**

---

## 5. Typical situations and how to handle them

**Situation 1: "Nothing happens after commit."**

Most likely, forgot to push. Check the "Push origin" button at the top.

**Situation 2: "The Pull button shows a number, but I'm still working on my stuff."**

First commit your own (step 3 in the cycle above), then pull. Pull may ask to allow a "merge" — usually the default button works.

**Situation 3: Conflict (merge conflict).**

This happens when you and someone else changed the same place in the same file simultaneously. GitHub Desktop will show the conflicted file and mark conflict lines with `<<<<<<<` and `>>>>>>>`.

**What to do:**
- Open the file in the editor
- Find the block:
  ```
  <<<<<<< HEAD
  your version
  =======
  their version
  >>>>>>> branch-name
  ```
- Decide what should be there (often — both versions combined), remove the markers
- Save the file
- Return to GitHub Desktop, commit with the message `resolve merge conflict`
- Push

If you're confused — call a developer. 5 minutes of two-person work.

**Situation 4: "I accidentally deleted a file / broke something important."**

Git remembers everything. In GitHub Desktop: History tab → find the commit where the file was still fine → right-click → Revert. This creates a new commit undoing the problematic changes.

If you're scared — call a developer, don't touch anything. The file will definitely come back.

**Situation 5: "GitHub Desktop wants me to make a branch."**

A branch is a parallel version of the files. For **most repository work this isn't needed** — write directly into main.

A branch is only needed if:
- You're preparing a big draft that shouldn't be visible yet
- You're working with a developer on a PR requiring review

In both cases — ask a developer to help create a branch. Once you do it, you'll understand.

---

## 6. Features live in GitHub Issues, not in the repo

This is the single biggest shift in v2. Features — what is being built, who owns it, what the scope is, what counts as success — live in **GitHub Issues**, not as markdown files in this repository.

When you have a feature idea, you do not create a file. You go to your team's code repository on GitHub.com, click "New Issue", pick the feature template, and fill it in: Why, Scope, Success criteria. That Issue *is* the spec, the tracker, and the discussion. No branch, no commit, no PR from your side. The browser is enough; the GitHub mobile app works too.

Concretely, this means:
- New idea → open an Issue.
- Discussion of a feature → in the Issue's comments.
- Feature shipped → the developer closes the Issue when merging the code PR. You don't mark anything anywhere.
- Looking for an old feature → search Issues with the closed filter; that's the historical record.

The repository in this document is for **durable knowledge**: ADRs, glossary, architecture, journal, research. GitHub Issues are for **live workflow**. Don't try to mirror Issue state in markdown — that copy will rot within a week.

---

## 7. What you can and can't do in the repo

This section is about the repository on your computer. For features, see section 6 — they don't live here.

**Can (and should) freely:**
- Create new files in `journal/feedback/` — it's an append-only folder, everyone writes there
- Create new files in `journal/retros/`
- Add new terms in `GLOSSARY.md`

**Ask a developer before changing:**
- ADRs (`decisions/`) — those are technical decisions; unrequested edits here look odd
- Technical patterns (`patterns/`) — same
- `AGENTS.md` — it's an instruction for AI, requires care
- Folder structure (creating new top-level folders)
- `STACK.md`, `TEAM.md` — declarative files

**Don't touch:**
- Files with extensions `.gitignore`, `.gitattributes` — those are plumbing
- The `.git/` folder (it's hidden anyway, but just in case)

### 6.1. If the team uses the knowledge module

If your team adopted the knowledge module (the `knowledge/` folder in the repository), it has separate rules. See section 9 of this document.

---

## 8. Alternative: editing via GitHub web

If GitHub Desktop feels scary, there's a simpler path: editing directly on github.com.

**How:**
1. Open the repository on GitHub.com
2. Click the needed file
3. Pencil icon (Edit) in the top right
4. Edit right in the browser
5. At the bottom — "Commit changes" field, fill in and save

Downsides:
- No Obsidian features (search, graph, backlinks)
- Inconvenient for long edits
- Can't create files in bulk

But for one-off edits like adding a glossary term or fixing a typo it works great. Especially from mobile. (For features, you don't need this at all — see section 6.)

### 8.1. When the repository is on your phone

Sometimes you need to note a thought or feedback when there's no computer.

**Options:**
- **Obsidian Mobile** + Obsidian Git plugin — syncs in background (requires some setup from a developer)
- **GitHub Mobile app** — you can open the repo, find a file, edit it right in the app
- **Just write in your phone's notes app**, transfer to the repository in the morning

Don't turn the repository into a phone task list. Serious work happens at a computer.

---

## 9. How to work with the knowledge module

*This section is only relevant if your team adopted the knowledge module (the `knowledge/` folder in the repository). If not, you can skip it.*

The knowledge module is where the team accumulates **external knowledge**: user interviews, competitor articles, research reports, call transcripts. AI (Claude or Cowork) automatically compiles these into structured wiki pages with cross-references.

**Why this matters to you:**
- You're likely one of the main people putting materials into `knowledge/raw/`
- You'll be able to ask questions like "what do we know about enterprise users?" and get a synthesis from all accumulated sources
- But there are strict rules you can't break, or the whole system falls apart

### 9.1. How to put sources into raw/

You work in the `knowledge/raw/` subfolder. Structure:

```
knowledge/raw/
├── user-interviews/
├── competitors/
├── industry/
└── calls/
```

Put a new file in the right folder. Filename: `YYYY-MM-DD-short-description.md`. For example:
- `knowledge/raw/user-interviews/2026-04-18-acme-corp-discovery.md`
- `knowledge/raw/competitors/2026-04-19-linear-new-pricing.md`

First lines of the file — frontmatter:

```markdown
---
type: user-interview
source: Acme Corp
date: 2026-04-18
author: @your-name
---

(rest of content — transcript, article text, notes)
```

Commit with message `raw: <what it is>` — e.g., `raw: Acme Corp discovery interview`.

Some time later, a developer (or you via Cowork) will run an ingest session, and Claude will add this material to wiki/.

### 9.2. Two iron rules

**Rule 1: `knowledge/raw/` — immutable.** Once you've put a file there, **you don't edit its contents**. Found an error? Add a new file with the correction (`2026-04-18-acme-corp-discovery-correction.md`). Don't touch the old one.

This matters because Claude remembers what it compiled from which source. If you silently change a source, the wiki starts diverging from reality.

**Rule 2: `knowledge/wiki/` — don't touch by hand.** This is Claude's zone. See something wrong in `wiki/personas/small-business-owner.md`? Don't edit directly. Instead:
- Either add a correction to raw/ and ask a developer to run ingest
- Or ask Claude via Cowork: "on page X the statement Y is wrong, re-verify the sources"

Editing the wiki directly — Claude will overwrite your edits next time, and you'll fight the system.

### 9.3. How to ask questions of the wiki

Through Cowork or Claude Code (ask a developer to show you how to connect Cowork to your repository — it's 5 minutes of setup).

Examples of questions that work:
- "What do we know about enterprise users? Give a brief description and quotes."
- "Which competitors come up in our interviews? Compare them on pricing."
- "Are there contradictions in what users say about onboarding?"

Claude goes into `wiki/`, returns an answer with links to raw sources. If a wiki page doesn't exist yet but raw has relevant stuff — Claude usually offers to create the page.

### 9.4. What to do when Claude makes something up

LLMs sometimes hallucinate — attribute a quote to the wrong interview, make generalizations that aren't in the sources.

If you're reading a wiki page and see a suspicious claim:
1. Click the `[[raw/...]]` link — the source opens
2. Check whether the claim is actually there
3. If not — tell Claude: "In wiki/personas/X it says Y with a link to raw/Z. I checked Z — it's not there. Update the page."

Claude will fix it. This is a normal cycle — don't panic.

### 9.5. Minimal ritual

Once a week (or more often if lots of new material) — an ingest session. Usually run by a developer or tech lead. For you, it's enough to:
- Put new raw/ files in during the week
- On ingest day, check Claude's report ("updated X, created Y, found Z contradictions")
- If something in the report concerns you — ask

Full documentation for the knowledge module is in `knowledge.md`. For you as a raw/ contributor, the above is enough.

---

## 10. Anti-patterns for non-technical users

**1. "I keep forgetting to pull, nothing works."**
Make it a habit: opened GitHub Desktop → first action is to look at "Pull origin".

**2. "Accumulated 30 changes and committed them all as 'update repo'."**
Small frequent commits — good. One big commit without description — bad. A month later nobody (including you) will remember what was in it.

**3. "I changed something in a developer's code, because 'well, it's just markdown'."**
The shared repository — yes, your zone. Code repositories — no. If something needs changing in a code repo's README — ask a developer.

**4. "I'm afraid to click something wrong and break everything."**
In git it's practically impossible to break something irreversibly. Any action can be undone. Ask, don't sit in fear.

**5. "I don't commit until I 'finish'."**
Better to save half your work than lose it in a crash. Intermediate commits are normal.

**6. "I edit wiki/ directly because there's an error there" (if using the knowledge module).**
See section 9.2. Never edit `knowledge/wiki/` directly — fix via sources or by asking Claude.

---

## 11. Realistic expectations

**First week:** you'll ask a developer 2–3 times a day. That's normal.

**One month in:** you comfortably do the pull → edit → commit → push cycle without help. Occasional conflicts you resolve yourself.

**One quarter in:** you don't think about git at all, it becomes a transparent background.

**What you definitely do NOT need to learn:** branches/merging without necessity, rebasing, cherry-picking, submodules, hooks, all the rest of "real" git plumbing. That's for developers. You need 4 operations, and that's it.

---

## Final

These 4 operations (clone / pull / commit / push), GitHub Desktop, and Obsidian are enough to be a full team member using Shubin Framework. You don't need to become a developer. You need to learn to quietly exchange files with the team so git doesn't get in the way.

If after a month you realize you want more control — learn branches and Pull Requests. But 95% of the work happens without them. Don't spend time on them in advance.

**When something goes wrong and you don't understand what happened** — don't stay silent, and don't do anything that could make it worse. Ask a developer. In git it's practically impossible to break something irreversibly, but you can create a tangled situation that's easier to untangle with fresh eyes.

Good luck. In a quarter, git will be a transparent background and you'll stop thinking about it.
