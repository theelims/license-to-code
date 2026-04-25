---
plan: ./plan.md
session_id: {YYYY-MM-DD}-{slug}
status: in-progress                  # in-progress | aborted | completed
started: {ISO 8601 timestamp}
last_updated: {ISO 8601 timestamp}
completed_at: null
aborted_at: null
abort_reason: null                   # e.g. "BUG-007 (critical): {title}"

last_completed_test: null            # T-NNN of the last test that received a [x], [!], or [~]

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

resume_directive_used: null          # null | resume | restart | reverify | subset
subset_test_ids: []                  # populated only when resume_directive_used = subset

# UX observations gathered during this run, awaiting consolidation into uat/ux-report.md.
# On clean completion the tester moves these into ux-report.md and clears this list.
# On abort, they remain here until the next run completes.
pending_ux_observations: []
# Each entry is shaped like:
# - category: NAV
#   test_id: T-005
#   observation: "Back button on /checkout returns to home instead of cart."
#   suggestion: "Preserve cart state on back navigation."
#   noted_at: {ISO 8601 timestamp}
---

# Session state — {session-id}

## Run log

This is an append-only log of every meaningful event in the session. Each line: `HH:MM TEST-ID outcome [bug ref] [note]`.

```
{HH:MM} T-001 pass
{HH:MM} T-002 pass
{HH:MM} T-003 fail → BUG-001 (medium)
{HH:MM} T-004 skip (depends on T-003 / BUG-001)
{HH:MM} T-005 pass
...
```

## Notes
{tester-written commentary; e.g. "Resumed from T-014 after BUG-007 fix.", or "Chrome lost connection between T-022 and T-023; reconnected and continued."}
