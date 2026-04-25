---
name: plan-review
description: Re-run the UAT plan reviewer subagent against an existing plan. Use when the user has hand-edited a plan and wants to re-validate consistency and preconditions without redoing the Q&A. Demotes plan status from approved to in-review and forces explicit re-approval.
disable-model-invocation: true
---

# UAT Plan Re-Review Skill

Re-run the `uat-plan-reviewer` subagent against an existing plan. Use this after hand-editing `plan.md`, or whenever the user wants to re-validate consistency and preconditions without redoing the Q&A.

---

## Workflow

### Step 1 — Resolve target plan
Interpret the user's argument as either:
- A session slug (e.g. `checkout-flow`, `2026-04-25-checkout-flow`) → resolve to `./uat/sessions/<match>/plan.md`. If multiple folders match, list them and ask.
- A direct path to a `plan.md` → use as-is.
- Empty → list all session folders under `./uat/sessions/` and ask the user which to review.

Verify the file exists. If not, stop and tell the user.

### Step 2 — Refuse on partial-execution plans
Read `plan.md`. If any test checkbox is `[x]`, refuse:
> This plan has executed tests. Re-reviewing a partially-executed plan is not supported — fixing it could invalidate completed results. Start a new session instead.

### Step 3 — Demote status
If the plan's `status` is `approved`, change it to `in-review`. Add a note to `review.md` (or create one) recording: "Re-review triggered at {timestamp} — status demoted from approved."

This forces the user to explicitly re-approve after the re-review, which is the point: hand-edits invalidate prior approval.

### Step 4 — Identify authoritative docs
Read the plan's frontmatter for the authoritative doc list. If missing or stale (paths no longer exist), ask the user.

### Step 5 — Invoke the reviewer
Call the `uat-plan-reviewer` subagent via the Task tool with the plan path and authoritative doc paths. The subagent appends to (or rewrites) `review.md`.

### Step 6 — Surface findings
In chat, summarize the review results exactly as the planning skill does:
- Counts of blockers / warnings / suggestions
- Each unresolved BLOCKER and WARNING with proposed fix options
- Remind the user: status is now `in-review`. Re-approve explicitly to make the plan executable again.

### Step 7 — On re-approval
When the user explicitly re-approves, set `status: approved` and update `approved_at` to the current timestamp.

---

## Output contract

After this skill runs, exactly one of:
- `plan.md` status is `in-review` and `review.md` has fresh findings the user must address, OR
- `plan.md` status is `approved` and `review.md` records the re-review pass.

No other files change. Do not auto-fix on re-review without user confirmation — at this point the user is in control of edits, and silent changes would defeat the purpose.
