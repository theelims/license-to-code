---
id: BUG-NNN
session: {YYYY-MM-DD}-{slug}
status: open                         # open | in-progress | fixed-pending-verify | verified | wontfix | duplicate
severity: medium                     # critical | high | medium | low
title: {imperative-mood title, e.g. "Submit button stays disabled after valid input"}
component: {area / route / module}
test_id: T-NNN
discovered: {ISO 8601 timestamp}
last_updated: {ISO 8601 timestamp}
duplicate_of: null                   # set to a BUG-NNN id if status is duplicate
related_bugs: []                     # list of BUG-NNN ids that share root cause or symptoms

# Tester-only fields below; do not edit manually
tester_run_id: {YYYY-MM-DD}-{slug}-run-{N}
chrome_url_at_failure: {url}
---

## Summary
{one short paragraph stating the bug in plain language}

## Steps to reproduce
1. {explicit, atomic action}
2. {explicit, atomic action}
3. {explicit, atomic action}

## Expected
{what the plan said should happen}

## Actual
{what was actually observed; describe both UI behavior and any signals from console/network}

## Evidence
- Screenshot: ./evidence/BUG-NNN-{short-name}.png
- Console: ./evidence/BUG-NNN-console.txt   *(omit if no relevant console output)*
- Network: ./evidence/BUG-NNN-network.har   *(omit if not captured)*

## Environment
- URL at failure: {url}
- Initial app state: {as described in plan, or any deviation noticed}
- Browser: Chrome (via claude-in-chrome extension)

## Reproducibility
- Attempts: {N}
- Reproduces: {always | intermittent (X/Y) | once-only}

## Notes
{anything the debug agent or human reviewer would want to know but doesn't fit above; one or two sentences}

---

## For the debug agent

When fixing this bug:
- Re-test by running `/uat-test {session-slug}` with the default `resume`, OR `reverify` to re-run from the top of the plan.
- After fix is verified, set `status: verified` here and update `bugs/INDEX.md`.
- If the fix introduces test-plan changes (e.g. expected behavior was wrong in the plan, not the app), open a new UAT session with `/uat-plan` rather than editing the existing approved plan.
