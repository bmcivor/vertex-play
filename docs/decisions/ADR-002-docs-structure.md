# ADR-002: Docs structure — epic directories with nested tickets

**Status:** Accepted
**Date:** 2026-04-10

## Context

Vertex-play has more upfront uncertainty than vertex-block (its sibling DNS blocker project). Vertex-block went design → flat `tickets.md` directly, with no epic layer. That worked for vertex-block because the design was largely settled before tickets were written.

Vertex-play needs a planning layer that:

- Groups related tickets into epics, since tickets alone don't carry the higher-level "this is the unit of work that delivers value" framing.
- Accommodates the **stop-at-uncertainty workflow**: outline → epics → order → tickets per epic → if a ticket cannot be written without guessing, stop and define a POC; resume after the POC.
- Lets ticket files be edited and committed independently (a flat single file is a merge-conflict trap and doesn't suit incremental editing across many sessions).
- Lines up cleanly with Taiga numbering when the vertex-play project is created in Taiga (Taiga numbers start at 1 for each project, so markdown ticket numbers should also start at 1 and be globally unique within the project).

## Decision

Adopt this layout:

```
docs/
  spec.md                     (existing, untouched outline)
  scope.md                    (existing)
  repo-boundaries.md          (existing)
  epics.md                    (new — short index, one paragraph per epic + ordering)
  epics/
    01-builder-image/
      epic.md                 (this epic's scope, open questions, POC link)
      tickets/
        001-foo.md            (one file per ticket)
        002-bar.md
    02-signing/
      epic.md
      tickets/
        003-foo.md
  pocs/                       (existing, empty — POC lab notes land here)
  reference/                  (existing — unchanged)
  implementation/             (existing — unchanged for now, see ADR-004)
  decisions/                  (new — ADRs from planning conversations)
```

Conventions:

- **Epic directories prefixed `01-`, `02-`, ...** for sort order. Ordering is also explicit in `epics.md`.
- **Ticket numbering is global** across the whole project (not per-epic), zero-padded to 3 digits, auto-incrementing as new tickets are added.
- **Tickets within an epic dir are not necessarily contiguous.** Epic 02 might contain `003`, `004`, `007`. This matches Taiga behaviour and is expected.
- This layout is **additive**. No existing files are moved or rewritten. `spec.md`, `scope.md`, `repo-boundaries.md`, `reference/`, and `implementation/` stay where they are.

## Consequences

- **Vertex-play diverges from vertex-block's flat `tickets.md` layout.** The ticket *format* is shared (see ADR-003); only the file layout differs.
- **When the Taiga project is created**, ticket `001-foo.md` maps to Taiga ticket #1, etc. The numbering survives the round-trip.
- **Epics are visible at three levels:** the top-level `epics.md` index (short), the per-epic `epic.md` (detail), and the `**Epic:**` field in each ticket (so context isn't lost when reading a ticket in isolation — see ADR-003).
- **`docs/decisions/` is part of this layout.** ADRs from planning conversations land there as one file per decision, named `ADR-NNN-short-slug.md`.
- **Existing `docs/reference/design-decisions.md`** captures four pre-planning decisions (toolchain location, instrumented tests, upload tool, branch model). It is left untouched. New decisions go to `docs/decisions/` as ADRs. The two streams are intentionally separate — merging them would be churn for no value.
