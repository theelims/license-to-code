---
# Plan-level fields (written by planner)
session_id: {YYYY-MM-DD}-{slug}
created: {ISO 8601 timestamp}
status: draft                       # draft | in-review | approved
approved_at: null
approved_by: null

scope_summary: |
  {one-paragraph description of what this UAT covers}

environment_url: http://localhost:3000

initial_app_state: |
  {description of the app state when testing starts: route, auth state, seeded data, etc.}

authoritative_docs:
  - path: docs/some-spec.md
    sections: [§2, §3.1]            # optional: limit to specific sections
  - path: docs/another-spec.md

context_docs:
  - path: docs/background.md

severity_overrides: null            # override default rubric; see below
out_of_scope:
  - {bullet list of what is intentionally not tested this session}

special_focus:
  - {bullet list of areas the user wants extra coverage on}

planner_notes: |
  {anything the planner wants the tester to know}

# Session-level fields (written by tester)
session_status: null                # in-progress | aborted | completed
session_started: null
session_completed_at: null
session_aborted_at: null
session_abort_reason: null          # e.g. "BUG-007 (critical): {title}"
last_completed_test: null           # T-NNN of the last [x], [!], or [~]

counts:
  passed: 0
  failed: 0
  skipped: 0
  pending: 0
bugs:
  critical: 0
  high: 0
  medium: 0
  low: 0

resume_directive_used: null         # null | resume | restart | reverify | subset
subset_test_ids: []

pending_ux_observations: []
# Each entry: - category: NAV
#               test_id: T-005
#               observation: "..."
#               suggestion: "..."
#               noted_at: {ISO 8601}
---

# UAT Plan: {session-slug}

> **Status:** `draft` → `in-review` → **`approved`** required before execution.
> The tester refuses to run a plan whose `status` is not exactly `approved`.

## Checkbox contract (test execution)

- `[ ]` not yet executed
- `[x]` passed
- `[!]` failed (see linked bug)
- `[~]` skipped (precondition unmet due to a prior failure)

---

## Group 1 — {e.g. Smoke}

### T-001 · {Short title}
- **Preconditions:** {state required, or `initial state`}
- **Postconditions:** {state after this test passes; "(none)" if pure read}
- **Steps:**
  1. {action}
  2. {action}
- **Expected:** {single observable outcome}
- **Covers:** docs/some-spec.md §2.1
- **Severity if failing:** medium  *(default; specify only if non-default)*
- [ ]

### T-002 · {Short title}
- **Preconditions:** T-001 passed (which establishes ...) AND ...
- **Postconditions:** ...
- **Steps:**
  1. ...
- **Expected:** ...
- **Covers:** docs/some-spec.md §2.2
- [ ]

---

## Group 2 — {e.g. Form validation}

### T-NNN · ...

---

## Group N — {e.g. Edge cases}

### T-NNN · ...

---

## Coverage map (filled by reviewer or planner)

| Authoritative requirement | Covered by |
|---|---|
| docs/some-spec.md §2.1 — login redirect | T-001 |
| docs/some-spec.md §2.2 — error message | T-002, T-007 |
| ... | ...

---

## Test execution session

> This section is written by the UAT tester skill. Do not edit by hand.

### Run log

Append-only event log. Each line: `HH:MM TEST-ID outcome [bug ref] [note]`.

```
{HH:MM} T-001 pass
{HH:MM} T-002 pass
{HH:MM} T-003 fail → BUG-001 (medium)
{HH:MM} T-004 skip (depends on T-003 / BUG-001)
```

### Notes

{tester-written commentary; e.g. "Resumed from T-014 after BUG-007 fix."}

---

## UX Observations

> Consolidated on clean completion by the tester. Deduplicated against existing entries.

### Category reference

| Code | Category | Examples |
|---|---|---|
| NAV | Navigation & routing | back button, breadcrumbs, deep links, route guards |
| FORM | Forms & inputs | validation, field behavior, autofill, submit states |
| VIS | Visual design | spacing, alignment, typography, color, hierarchy |
| PERF | Performance | load time, perceived speed, jank, layout shift |
| A11Y | Accessibility | keyboard nav, focus order, ARIA, contrast, screen-reader |
| IA | Information architecture | grouping, labeling, content order, discoverability |
| COPY | Copywriting | wording, tone, clarity, error messages, microcopy |
| FEEDBACK | User feedback | loading states, success/failure indicators, confirmations |
| ERROR | Error handling UX | how errors are communicated (not the bugs themselves) |
| MOBILE | Mobile / responsive | breakpoints, touch targets, mobile-only issues |
| OTHER | Anything that doesn't fit | use sparingly |

### Entry format

```markdown
- [ ] **NAV-003** Back button on /checkout returns to home instead of cart.
  *Suggestion:* Preserve cart state on back navigation.
  *Sessions:* 2026-04-25-checkout-flow, 2026-05-02-cart-redesign
```

- IDs are stable and append-only within each category.
- Checkboxes (`[x]`) belong to the user. The tester never checks them.
- When the same observation surfaces in multiple sessions, append the new slug to `Sessions:`.

### NAV — Navigation & routing

*(No observations yet.)*

### FORM — Forms & inputs

*(No observations yet.)*

### VIS — Visual design

*(No observations yet.)*

### PERF — Performance

*(No observations yet.)*

### A11Y — Accessibility

*(No observations yet.)*

### IA — Information architecture

*(No observations yet.)*

### COPY — Copywriting

*(No observations yet.)*

### FEEDBACK — User feedback

*(No observations yet.)*

### ERROR — Error handling UX

*(No observations yet.)*

### MOBILE — Mobile / responsive

*(No observations yet.)*

### OTHER

*(No observations yet.)*
