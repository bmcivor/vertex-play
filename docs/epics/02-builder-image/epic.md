---
type: epic
project: vertex-play
---

## E2 — Builder image

### Purpose

Produce a reproducible Android builder Docker image in `shadowlands:5000`, built by Jenkins (not a developer laptop), and validate that it can build a real Android app into an unsigned AAB inside a container.

This is POC-A from `spec.md`. The existing plan in `docs/implementation/builder-image.md` serves as the detailed POC plan for this epic (see ADR-004). Tickets reference tasks and gates from that plan rather than duplicating content.

### In scope

- Verify remaining lab prerequisites not covered by E1 (P7–P9: disk, RAM, outbound HTTPS)
- Resolve all version pins (G1–G8 from builder-image.md)
- Write `docker/Dockerfile` with pinned JDK / SDK / cmdline-tools / build-tools
- Write the bootstrap `Jenkinsfile` that builds and pushes the image to `shadowlands:5000`
- Trigger a Jenkins build and verify the image lands in the registry
- End-to-end validation: build `test-app/` (from E3) inside the image, produce an unsigned AAB
- Write the POC-A lab note

### Out of scope

- Signing (E5)
- Play upload (E6)
- The reference app-pipeline Jenkinsfile (E7) — the bootstrap Jenkinsfile here builds vertex-play's own builder image, not app builds
- Jenkins credentials for signing or Play API (E5 / E6)

### Validation target

ADR-001 changed the validation target from `android/architecture-samples` to `vertex-play/test-app/`. The version pin resolution in this epic (ticket 006) drives the AGP/JDK/SDK choices; E3 writes the test app to match. The end-to-end validation step (ticket 009) depends on E3 completing.

### Dependencies

- E1 (Jenkins sees vertex-play — tickets 001–004)
- E3 (test app — for end-to-end validation only; earlier tickets can proceed in parallel)

### Blocks

- E5 (signing builds on POC-A findings)
- All subsequent epics that use the builder image
