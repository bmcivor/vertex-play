# E7 — Reference app pipeline

**Status:** Tickets written.

## Purpose

Write the reference `Jenkinsfile` that Android app repos copy and adapt. This is the primary deliverable of vertex-play: a working, documented pipeline definition that covers all three branch triggers, wires all stages conditionally, and derives `versionCode` from Jenkins `BUILD_NUMBER`.

This epic synthesises what E2, E5, and E6 proved in throwaway jobs into a single coherent pipeline definition.

## Key decisions

- **Consumption model:** Copy-paste — see [ADR-006](../../decisions/ADR-006-consumption-model-copy-paste.md)
- **`versionCode` derivation:** Jenkins `BUILD_NUMBER` — monotonically increasing by design, no per-release discipline required

## In scope

- Reference `Jenkinsfile` at the repo root covering all three branch triggers
- `versionCode` injected from `BUILD_NUMBER`
- End-to-end validation of all three triggers against test-app
- E7 lab note

## Out of scope

- Jenkins Shared Library infrastructure (ADR-006)
- App repos themselves — vertex-play contains tooling only; app source lives in separate repos
- Production / closed testing track promotion — manual Play Console action, not automated
- POC-D (instrumented tests / emulator) — optional, deferred per `design-decisions.md`

## Branch model (from spec.md)

| Trigger | Stages |
|---------|--------|
| Pull request | Build + test only |
| Merge to `main` | Build + test + sign |
| Git tag | Build + test + sign + upload to internal track |

## Dependencies

- E2 (builder image in registry)
- E5 (signing config and credentials proven)
- E6 (upload tooling proven, POC-C complete)

## Blocks

- E8 (handoff docs reference this Jenkinsfile)

## Tickets

- [026 — Write reference Jenkinsfile](tickets/026-write-reference-jenkinsfile.md)
- [027 — End-to-end validation of all three triggers](tickets/027-end-to-end-validation.md)
- [028 — Write E7 lab note](tickets/028-write-e7-lab-note.md)
