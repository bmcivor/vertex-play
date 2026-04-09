# ADR-003: Ticket format — vertex-block format plus Epic field

**Status:** Accepted
**Date:** 2026-04-10

## Context

Vertex-block already has a ticket format documented in `vertex-block/docs/tickets.md`. It is designed to be copied into Taiga manually and uses: user story + acceptance criteria + dependencies + priority. The format works well in vertex-block.

Vertex-play needs a ticket format too. The simplest path is to reuse vertex-block's so the lab has **one ticket convention** rather than two near-identical-but-not-quite formats.

The only difference: vertex-play has an epic layer (see ADR-002) that vertex-block does not. Tickets should be visibly tagged with their epic so the relationship isn't lost when looking at a single ticket file in isolation. The epic dir on disk already provides this, but having it inside the file removes a cross-reference step.

## Decision

Use vertex-block's exact ticket format, **plus one additional line: `**Epic:** En — name`** placed near the top of each ticket file.

Format:

```markdown
## N. Title

**Epic:** E2 — Builder image

**As a** [role]
**I want** [goal]
**So that** [reason]

### Acceptance criteria

- bullet
- bullet

### Dependencies

- N (or "None")

### Priority

- High | Medium | Low
```

Cancelled tickets follow vertex-block convention: `## ~~N. Title~~ (Cancelled)` with a short reason underneath.

## Consequences

- **One mental model of "what a ticket looks like"** across vertex-block and vertex-play.
- **The `**Epic:**` field is redundant with the file's location** (epic directory) but visible without cross-referencing — acceptable redundancy.
- **If/when Taiga writes are scripted**, both vertex-block and vertex-play tickets can be parsed by the same code. The `**Epic:**` field maps to a Taiga epic if Taiga has them, or is ignored otherwise.
- **The ticket numbering scheme** (global, zero-padded, auto-incrementing) is part of ADR-002 — this ADR only covers the *content* format inside each ticket file.
