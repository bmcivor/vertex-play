# E2 — Builder image

**Status:** Tickets written.

## Purpose

Produce a reproducible Android builder Docker image in `shadowlands:5000`, built by Jenkins (not a developer laptop), and validate that it can build a real Android app into an unsigned AAB inside a container.

This is POC-A from `spec.md`. The existing plan in [`docs/implementation/builder-image.md`](../../implementation/builder-image.md) serves as the detailed POC plan for this epic (see [ADR-004](../../decisions/ADR-004-builder-image-md-retained.md)). Tickets reference tasks and gates from that plan rather than duplicating content.

## In scope

- Verify remaining lab prerequisites not covered by E1 (P7–P9: disk, RAM, outbound HTTPS)
- Resolve all version pins (G1–G8 from builder-image.md)
- Write `docker/Dockerfile` with pinned JDK / SDK / cmdline-tools / build-tools
- Write the bootstrap `Jenkinsfile` that builds and pushes the image to `shadowlands:5000`
- Trigger a Jenkins build and verify the image lands in the registry
- End-to-end validation: build `test-app/` (from E3) inside the image, produce an unsigned AAB
- Write the POC-A lab note

## Out of scope

- Signing (E5)
- Play upload (E6)
- The reference app-pipeline Jenkinsfile (E7) — the bootstrap Jenkinsfile here builds vertex-play's own builder image, not app builds
- Jenkins credentials for signing or Play API (E5 / E6)

## Validation target

ADR-001 changed the validation target from `android/architecture-samples` to `vertex-play/test-app/`. The version pin resolution in this epic (ticket 006) drives the AGP/JDK/SDK choices; E3 writes the test app to match. The end-to-end validation step (ticket 009) depends on E3 completing.

## Dependencies

- E1 (Jenkins sees vertex-play — tickets 001–004)
- E3 (test app — for end-to-end validation only; earlier tickets can proceed in parallel)

## Blocks

- E5 (signing builds on POC-A findings)
- All subsequent epics that use the builder image

## Tickets

- [005 — Verify remaining lab prerequisites (P7–P9)](tickets/005-verify-remaining-prerequisites.md)
- [006 — Resolve version pins (G1–G8)](tickets/006-resolve-version-pins.md)
- [007 — Write Dockerfile and bootstrap Jenkinsfile](tickets/007-write-dockerfile-and-jenkinsfile.md)
- [008 — Trigger Jenkins build and verify image in registry](tickets/008-trigger-build-and-verify-image.md)
- [009 — End-to-end validation: build test app inside image](tickets/009-end-to-end-validation.md)
- [010 — Write POC-A lab note](tickets/010-write-poc-a-lab-note.md)
