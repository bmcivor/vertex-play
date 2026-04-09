# ADR-004: `builder-image.md` retained until POC-A runs

**Status:** Accepted
**Date:** 2026-04-10

## Context

`docs/implementation/builder-image.md` is a ~290-line POC-A plan written before this planning session. It contains structured content that maps onto the new epic/ticket workflow:

- Prerequisites checklist (P1–P9)
- Version-pin lookup gates (G1–G8)
- Task sequence (T0–T8)
- Acceptance criteria
- Risk register

It is, in shape, exactly the "mini-project plan for a POC" pattern that the new vertex-play workflow describes — just written before the workflow was formalised, in a different layout.

When the new structure (`epics.md`, `epics/01-builder-image/`, ...) lands, `builder-image.md` becomes **structurally orphaned**. The question: rewrite it now into the new format, retire it, or leave it.

## Decision

**Leave `builder-image.md` in place until POC-A actually runs.**

The E2 (builder image) epic file will reference it as "POC-A plan, see `docs/implementation/builder-image.md`." After POC-A runs and produces `docs/pocs/poc-a.md` (the lab note), explicitly revisit whether `builder-image.md` still earns its place — its contents may be absorbed into E2's tickets, or it may stay as historical context.

## Consequences

- **The new epic structure has one referenced-but-unmoved file in `docs/implementation/`**, which is mildly inelegant. Acceptable cost.
- **E2 tickets will reference `builder-image.md`**, not duplicate its content. Avoids the wall-of-text trap of restating the same plan in two places.
- **`builder-image.md` currently uses `android/architecture-samples` as the validation target**, but ADR-001 changed this to `vertex-play/test-app/`. This discrepancy will be resolved when E2 tickets are written — at that point, the relevant sections of `builder-image.md` may be updated to point at `test-app/`, or the new ticket may simply override them. Flagged but not fixed now.
- **After POC-A's lab note exists**, an explicit review decides: absorb `builder-image.md` into E2 tickets, retire it, or keep it as historical context. That decision becomes a new ADR.
- **Rewriting documentation purely to fit a new structure is busywork** of exactly the kind this workflow is designed to prevent. Premature rewriting would be a wall-of-text trap in another form.
