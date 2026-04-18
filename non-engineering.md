# Shubin Framework: Non-engineering concerns

**Full title:** Minimum on Finance, Legal, Security, Data and Support for a team of 2–8
**Part of:** Shubin Framework (companion document)
**Version:** 1.0

**Who it's for:** tech lead or founder of a 2–8 team pre-PMF and pre-stable-traction. The document answers: "what's the minimum I need to do in these areas to avoid catastrophe, and what can be deferred".

**What it is:** a short set of minimal practices for areas outside core engineering that still need attention.

**What it isn't:** a full playbook for Finance, Legal, Compliance or Security. For teams with compliance-sensitive enterprise customers, this is not enough — you need real specialists there.

**Related documents:**
- `framework.md` — full framework

---

## Philosophy

Finance, Legal, Compliance, Support, Data for a 2–8 team are **not your main activity**. Full practices are needed after you've found PMF, collected traction, and started hiring. Until then — a minimal set.

The main mistake at this stage is one of two extremes:
1. **Cargo cult enterprise.** A team of 3 does annual security reviews, SOC2, threat modelling, $2K/mo legal tool subscriptions. Result: budget eaten, actually-important things not done.
2. **"PMF first, all this later".** Zero security practices, passwords in git, privacy policy copied from a competitor. The first incident is catastrophic.

This document is about the middle path. A minimum that actually protects, without theater.

---

## 1. Finance

What you actually need:

**Table of all service subscriptions** in the vault or in Google Sheets. What you pay, how much, to whom, when renewal, who owns it. Reviewed quarterly.

```markdown
| Service | Plan | $/month | Owner | Purpose | Renewal |
|---------|------|---------|-------|---------|---------|
| Vercel | Pro | 20 | @alice | Frontend | monthly |
| Supabase | Pro | 25 | @bob | DB | monthly |
```

**Burn rate and runway** in Google Sheets. Reviewed monthly by the founder. If runway < 12 months — start thinking about fundraising or cutting costs. < 9 — act actively. < 6 — cut costs without delay.

**Unit economics at pre-PMF stage** is fantasy with two unknowns. You don't know LTV without stable churn history, true CAC without a stable funnel. Don't spend time on beautiful LTV/CAC = 6.2x until real stable churn exists. Pre-PMF, knowing ARPU, gross margin, burn is enough. That's sufficient for sanity checks.

**Significant financial decisions** (pricing, major spending) are ADRs. See `framework.md` chapter 3.

---

## 2. Legal

Minimum for SaaS:

**Privacy Policy and Terms of Service.** Don't write from scratch with a lawyer ($500–2000 and weeks). Start with templates (Termly, iubenda, GetTerms) for $10–30, adapt to your product. With a lawyer, adapt when the first enterprise customers appear.

**IP assignment clause** in all contracts with employees and contractors. Without it, the code they write is legally not yours. It's a single line in the contract; without it — a serious problem during fundraising or acquisition.

**GDPR data export and deletion** as technical features, if you have EU users. That's 1–2 sprints of work, not a compliance program. All you need:
- User can request a dump of their data (export) — you send JSON or CSV
- User can request to delete all data (deletion) — you actually delete, not soft-delete

**What NOT to do yet:**
- SOC 2 — only when enterprise customers require it (usually at $1M+ ARR)
- ISO 27001 — when doing enterprise in Europe
- HIPAA — when handling health data
- Trademarks — when you have revenue and protection needs ($500–2000 + 6–12 months of process)
- Patents — almost never for a software startup, exception for deep-tech

If your product touches money (payments, crypto, lending) or health — the legal minimum is significantly larger, not covered by this document, hire a specialist.

---

## 3. Security

### 3.1. Minimum from day one

**Secrets in a manager, not in git.** Doppler, 1Password Secrets Automation, or equivalent. Never in git even in a private repo. Never in Slack/Discord. Different secrets for dev/staging/prod.

**Dependabot or Renovate** for automatic dependency updates. Supply chain attacks are a real threat in 2026.

**HTTPS everywhere.** In 2026 this is automatic via hosting platforms (Vercel, Cloudflare). Verify you haven't forgotten anywhere.

**Passwords through argon2 or bcrypt**, never plain text. If you use managed auth (Supabase, Auth0, Clerk) — it's done for you.

**Row-Level Security in Postgres** (or equivalent in another DB) for any multi-user system. One of the most common causes of leaks is "query without WHERE user_id = ...".

**OWASP Top 10 quick checklist pass** — half a day to read the checklist and check against it. Closing discovered holes is separate work, may take a few days, but it's a one-time thing.

**Privacy policy and basic GDPR compliance** (see section 2) — not just legal, it's also security hygiene.

All of this is 1–2 weeks of work in the first month, with returns for small follow-ups. Not 1 day, as often promised. And not 3 months.

### 3.2. Secrets rotation — honestly

Advice like "rotate every 90 days" sounds great, but in a team of 4 it gets skipped and you live in an illusion of rotation. Better:
- Real rotation every 6 months — and actually do it
- Mandatory rotation on team turnover (person left → prod secrets rotated within 24 hours)

### 3.3. Incident response

Minimum process (written in `log/incidents/README.md`):
1. Who can declare an incident (any team member)
2. Who becomes the owner (usually tech lead)
3. Communication channel (separate Slack channel during an incident)
4. Priorities: contain → eradicate → recover → learn
5. Postmortem within 72 hours — mandatory, blameless

Each incident → entry in `log/incidents/YYYY-MM-DD-name.md` with timeline, root cause, prevention.

### 3.4. What NOT to do yet

Threat model, SBOM, annual pen-test, bug bounty, full security review — for teams of 15+ or serious money/health handling. Before that, it's over-engineering, time spent on documents that don't change behavior.

Exceptions (when the security minimum is larger): fintech, medtech, any handling of client money. There it's not a companion document, it's core engineering — hire a security specialist.

---

## 4. Data and analytics

### 4.1. The only critical thing from day one — event taxonomy

Without it, six months later you have a dump of `user_signup`, `user_signed_up`, `signup_completed`, `new_user_registered` — all of different ages and slightly different semantics. Nobody remembers how they differ. Analytics lies.

Put in `stable/events.md`:

```markdown
# Event Taxonomy

## Naming rules
- snake_case
- <object>_<past_tense_action>
- Examples: user_signed_up, subscription_cancelled, invoice_paid

## Required properties on any event
- user_id (or anonymous_id if not authenticated)
- session_id
- timestamp
- source (web, mobile, api)

## Events

### user_signed_up
When: after email confirmation
Properties: plan, referrer, signup_method

### subscription_created
When: after first successful payment
Properties: plan, amount, currency
```

### 4.2. Minimal analytics stack

- **PostHog** for product analytics (or Amplitude, Mixpanel — pick one)
- **Plausible / Fathom** for web analytics (privacy-first, cheaper than GA)
- **Sentry** for error tracking (already in security minimum)

One product analytics, not three. Otherwise data diverges.

### 4.3. North Star and supporting

`stable/north-star.md` — **one** main metric, 3–5 leading indicators. Not 15. Fifteen metrics is the absence of metrics.

North Star examples:
- SaaS: Weekly Active Users engaged in the core action
- Marketplace: Transactions completed per week
- Media: Minutes consumed per user per week

### 4.4. About cost

PostHog at scale starts getting expensive ($450+/mo at 1M events). Budget a year ahead. A cheap alternative — self-hosted PostHog on a $20 VPS, but that's operational debt.

### 4.5. What NOT to do yet

- Data warehouse (Snowflake, BigQuery) — not needed before 100K MAU or $1M ARR
- ETL pipelines (Airbyte, Fivetran) — even less so
- A separate Data role — only after 10K+ users and real data questions emerging

For analytics at MVP stage, PostHog built-in dashboards + ad-hoc SQL when needed is enough.

---

## 5. Support

### 5.1. When you need dedicated support

- Up to 20 tickets/week — developers take turns, or the founder. Slow, but gives direct signal.
- 20–100 tickets/week — first support engineer (often part-time).
- 100+ — dedicated team + self-service knowledge base.

### 5.2. Feedback loop — mandatory

The most important thing from support is **the link to the product**. Support sees more user pain than PM and developers combined. That pain needs to make it into the vault.

Workflow:
1. Support agent receives a ticket.
2. If it's a signal (not one-off), creates an entry in `log/feedback/YYYY-MM-DD-<theme>.md` with description and link to the ticket.
3. Once a week the PM scans these files and turns patterns into hypotheses in `active/features/` or in the wiki (if the knowledge module is in use).

Without this loop, you build features blind.

### 5.3. Triage by priority

Minimum:

```markdown
## P0 — critical (damage/downtime/security)
Response: 1 hour, all hands

## P1 — feature broken for many users
Response: 4 hours, same day resolution

## P2 — broken for one user, workaround exists
Response: 24 hours, next sprint

## P3 — feature request, UX improvement
Response: 48 hours, prioritized into roadmap
```

These are **internal** SLAs, not public.

### 5.4. Public SLAs — careful

Public SLAs (99.9% uptime, 30-minute P0 response) — **only when you're ready to pay for violation**. Otherwise it's a marketing promise that compromises trust on the first breach.

A team of 4 can't guarantee 30-minute P0 response without on-call rotation with minimum 3 people. If you don't have on-call rotation — don't promise it.

---

## 6. Compliance

For a 2–8 team at pre-PMF stage — no compliance program. Compliance requirements come from specific clients or regulators. Until they exist, you don't know what's actually needed, and any "compliance preparation" will be wasted time.

The only things worth doing proactively:
- **GDPR data export/deletion** — if EU users (see Legal)
- **Subprocessors list** — all parties you pass data to (Vercel, Supabase, PostHog, etc). Needed for the first enterprise client and for a correct privacy policy.
- **Access log for production secrets** — who and when looked at prod secrets. Simple table in the vault, updated on each access.

Everything else (SOC 2, ISO, HIPAA) — when a specific client asks.

---

## 7. Integration with the main framework

- **Financial, Legal, Security ADRs** live in common `active/decisions/`, numbered sequentially with technical ones.
- **Event taxonomy** — in `stable/events.md`.
- **Subscriptions table** — either in `stable/subscriptions.md` or in a separate Google Sheet with a link.
- **Incidents** — in `log/incidents/`.
- **Feedback from support** — in `log/feedback/`.
- **Security guides**, if they grow, — in `active/patterns/security/`.

All of this fits organically into the `stable/active/log/` structure. No need to create separate top-level folders `finance/`, `legal/`, `security/` — that's cargo cult.

---

## 8. Quarterly review

Once a quarter during the strategy offsite (see `framework.md` chapter 10) — 30 minutes on non-engineering concerns:

- Subscriptions table — anything to cancel
- Runway — where we are
- Privacy policy / terms — any product changes requiring updates
- Security — were there incidents, what did we learn
- North Star — still relevant

No separate security review, separate compliance review, separate finance review — all in 30 minutes within the common offsite. For a team of 2–8 that's enough.

---

## Final

Non-engineering concerns for a 2–8 team are an area where **less is often more**. Each extra practice is time subtracted from building the product. Each missing critical practice is a risk of catastrophe.

This document is an attempt to draw the line between critical minimum and over-engineering. If your product is specific (fintech, medtech, enterprise deal from day one) — the line shifts, and you need specialists, not a companion document.

For typical B2B or B2C SaaS pre-$1M ARR — this is enough.
