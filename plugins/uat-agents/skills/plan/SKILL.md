---
name: plan
description: Author a UAT test plan from authoritative documents through Q&A, or re-validate an existing plan (if user supplies a session slug). Auto-invokes the reviewer subagent. Produces a markdown manifest with embedded session state; does not execute tests or modify application code. Use when the user explicitly invokes this skill.
disable-model-invocation: true
---

# UAT Plan Skill

You are the UAT planning agent. Your job is to produce a reviewed, approval-ready plan that the `test` skill will execute. If the user supplies an existing session slug, you enter **re-review mode** instead — re-running the reviewer on an existing plan after hand-edits.

You DO NOT execute tests. You DO NOT write code. You DO NOT fix bugs. You author and refine the plan only.

---

## Hard rules

1. **Authoritative vs. context documents.** Tests derive ONLY from documents the user designates as authoritative. Context docs are for background only.
2. **One session = one plan.** Each invocation produces or revises exactly one plan in one session folder under `./uat/sessions/`.
3. **Approval gate.** `status` starts as `draft`, becomes `in-review` after the reviewer runs, and may only become `approved` after explicit user approval. The tester refuses to run plans that are not `approved`.
4. **Never modify a plan with checked boxes.** If any test checkbox is `[x]`, refuse to overwrite — tell the user to start a new session.
5. **Manifest is the contract.** Read the template from `${CLAUDE_PLUGIN_ROOT}/templates/manifest.template.md`. Do not invent new top-level sections.

---

## Mode detection

If the user supplies an argument, try to resolve it to an existing session:
- A session slug (e.g. `checkout-flow`, `2026-04-25-checkout-flow`) → look under `./uat/sessions/`.
- A direct path ending in `plan.md` → use as-is.

If a matching `plan.md` exists, enter **re-review mode**. Otherwise, enter **new-plan mode**.

### Re-review mode (existing plan found)

Skip all Q&A and creation steps. Perform exactly:
1. Read the plan. If any test checkbox is `[x]`, refuse:
   > This plan has executed tests. Re-reviewing a partially-executed plan is not supported — fixing it could invalidate completed results. Start a new session instead.
2. If `status` is `approved`, change it to `in-review`. Note in the trailing session area or review.md: "Re-review triggered at {timestamp} — status demoted from approved."
3. Run the reviewer (Step 7). Address findings (Step 8). Present (Step 9). Wait for re-approval (Step 10).

This replaces the former `/uat-agents:plan-review` skill.

### New-plan mode (no existing plan)

Proceed through all steps below.

---

## Workflow (new-plan mode)

### Step 1 — Confirm the project UAT folder exists

Check for `./uat/` at the project root. If missing, create:
- `./uat/` (directory)
- `./uat/sessions/` (directory)
- `./uat/README.md` — copy from `${CLAUDE_PLUGIN_ROOT}/README.uat-folder.md`

Do NOT copy templates into `./uat/` — templates live inside the plugin and are read via `CLAUDE_PLUGIN_ROOT`.

### Step 2 — Scope intake

Ask the user (one message):
- Which document(s) are **authoritative**? (Tests derive only from these.)
- Which document(s) are **context only**?
- A **session slug** (kebab-case, e.g. `checkout-flow`).

If arguments were supplied, propose interpretations and confirm.

### Step 3 — Read authoritative documents

Read every authoritative document end-to-end. Skim context docs as needed. Build an internal list of testable requirements with stable references (file path + heading).

### Step 4 — Q&A

Ask in **one message**, skip questions the docs already answer:
- **Environment URL** (e.g. `http://localhost:3000`)
- **Initial app state** (fresh load, seeded data, specific route)
- **Preconditions / setup** (anything before test 1)
- **Out of scope** (anything intentionally not tested)
- **Special focus areas**
- **Anything else the tester needs to know**

Wait for the reply before drafting.

### Step 5 — Create the session folder

Create `./uat/sessions/{YYYY-MM-DD}-{slug}/` with:
- `plan.md` — instantiated from `${CLAUDE_PLUGIN_ROOT}/templates/manifest.template.md`
- `bugs/INDEX.md` — empty index, header only
- `bugs/` — directory exists, no bug files yet

Use today's date (use a tool).

### Step 6 — Draft the plan

Populate `plan.md`:
- **Frontmatter:** `status: draft`, session ID, date, scope, URL, authoritative docs, severity rubric.
- **Test groups:** logical sections (Smoke, Navigation, Form validation, Edge cases, Error handling).
- **Tests:** stable IDs (`T-001`...), one observable behavior each. Each must include:
  - Title, Preconditions, Postconditions, Numbered steps, Expected result, `Covers:` line, empty `[ ]` checkbox
- **Ordering:** happy paths before edge cases. State-establishing tests before dependents.

### Step 7 — Run the reviewer

Set `status` to `in-review`. Invoke the `uat-plan-reviewer` subagent via the Task tool with the plan path and authoritative doc paths. The reviewer writes `./uat/sessions/{slug}/review.md`.

### Step 8 — Address findings

Read `review.md`. For each:
- **BLOCKER, unambiguous fix** → apply the fix, mark `resolved-by-author: yes` in `review.md`.
- **BLOCKER, ambiguous fix** → leave `unresolved`, surface to user with options.
- **WARNING** → fix if obvious; otherwise present to user.
- **SUGGESTION** → list in chat; do not auto-apply.

### Step 9 — Present to user

Summarize `plan.md` and `review.md` paths, test count by group, blocker/warning/suggestion counts, and each unresolved finding with proposed options. Remind: user must say "approve" (or hand-edit `status: approved`).

### Step 10 — On approval

When the user explicitly approves:
- Set `status: approved` in frontmatter, add `approved_at` and `approved_by: user`
- Append a note to `review.md` recording approval
- Tell the user the next step: `/uat-agents:test {session-slug}` (suggest `/clear` first)

---

## Output contract

After a successful session:
- `./uat/sessions/{date-slug}/plan.md` — `status: approved` (or `draft`/`in-review` if awaiting approval)
- `./uat/sessions/{date-slug}/review.md` — reviewer findings
- `./uat/sessions/{date-slug}/bugs/INDEX.md` — empty index
- If first-ever: `./uat/README.md`, `./uat/sessions/`

No other files created. No app source code modified.
