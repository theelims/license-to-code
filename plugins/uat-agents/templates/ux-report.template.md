# UX Observations & Suggestions

This is the cumulative idea repository. The UAT tester appends to it on clean completion of each session, deduplicating against existing entries. The user (you) curates it and uses it as input for future GSD phases.

## How this works

- **Categories use stable nomenclature** (defined below). The tester classifies each observation into exactly one.
- **IDs are stable and append-only.** Once `NAV-014` is assigned, it never gets renumbered. New observations get the next free ID in their category.
- **Checkboxes belong to the user.** Check `[x]` when an observation has been addressed in a later GSD phase. The tester never checks them.
- **Sessions field tracks provenance.** When the same observation surfaces in multiple sessions, the tester appends the new session slug rather than creating a duplicate entry.
- **Merge ambiguity → ask.** If the tester is uncertain whether a new observation matches an existing one, it asks the user before merging.

## Category nomenclature

| Code | Category | Examples |
|---|---|---|
| NAV | Navigation & routing | back button, breadcrumbs, deep links, route guards |
| FORM | Forms & inputs | validation, field behavior, autofill, submit states |
| VIS | Visual design | spacing, alignment, typography, color, hierarchy |
| PERF | Performance | load time, perceived speed, jank, layout shift |
| A11Y | Accessibility | keyboard nav, focus order, ARIA, contrast, screen-reader behavior |
| IA | Information architecture | grouping, labeling, content order, discoverability |
| COPY | Copywriting | wording, tone, clarity, error messages, microcopy |
| FEEDBACK | User feedback | loading states, success/failure indicators, confirmations |
| ERROR | Error handling UX | how errors are communicated to the user (not the bugs themselves) |
| MOBILE | Mobile / responsive | breakpoints, touch targets, mobile-only issues |
| OTHER | Anything that doesn't fit | use sparingly |

---

## NAV — Navigation & routing

*(No observations yet.)*

## FORM — Forms & inputs

*(No observations yet.)*

## VIS — Visual design

*(No observations yet.)*

## PERF — Performance

*(No observations yet.)*

## A11Y — Accessibility

*(No observations yet.)*

## IA — Information architecture

*(No observations yet.)*

## COPY — Copywriting

*(No observations yet.)*

## FEEDBACK — User feedback

*(No observations yet.)*

## ERROR — Error handling UX

*(No observations yet.)*

## MOBILE — Mobile / responsive

*(No observations yet.)*

## OTHER

*(No observations yet.)*

---

## Entry format (for reference)

```markdown
- [ ] **NAV-003** Back button on /checkout returns to home instead of cart.
  *Suggestion:* Preserve cart state on back navigation.
  *Sessions:* 2026-04-25-checkout-flow, 2026-05-02-cart-redesign
```
