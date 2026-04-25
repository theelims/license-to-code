---
session_id: {YYYY-MM-DD}-{slug}
created: {ISO 8601 timestamp}
status: draft                       # draft | in-review | approved
approved_at: null
approved_by: null

scope_summary: |
  {one-paragraph description of what this UAT covers}

environment_url: http://localhost:3000

initial_app_state: |
  {description of the app state when testing starts: route, auth state (none, per system), seeded data, etc.}

authoritative_docs:
  - path: docs/some-spec.md
    sections: [§2, §3.1]            # optional: limit to specific sections
  - path: docs/another-spec.md

context_docs:
  - path: docs/background.md

severity_overrides: null            # set to a list to override default rubric; see uat/README.md

out_of_scope:
  - {bullet list of what is intentionally not tested this session}

special_focus:
  - {bullet list of areas the user wants extra coverage on}

planner_notes: |
  {anything the planner wants the tester to know that doesn't fit elsewhere}
---

# UAT Plan: {session-slug}

> **Status:** draft → in-review → **approved required before execution**
>
> Tester refuses to run this plan unless `status: approved` in the frontmatter.

## How to read this plan

- Each test has a stable ID (`T-NNN`), preconditions, postconditions, numbered steps, expected result, and a `Covers:` line tracing back to authoritative docs.
- Checkbox markers used by the tester:
  - `[ ]` not yet executed
  - `[x]` passed
  - `[!]` failed (see linked bug)
  - `[~]` skipped (precondition unmet because of a prior failure)

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
...

---

## Group N — {e.g. Edge cases}

### T-NNN · ...
...

---

## Coverage map (filled by reviewer or planner)

| Authoritative requirement | Covered by |
|---|---|
| docs/some-spec.md §2.1 — login redirect | T-001 |
| docs/some-spec.md §2.2 — error message | T-002, T-007 |
| ... | ... |
