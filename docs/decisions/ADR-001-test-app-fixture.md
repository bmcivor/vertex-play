# ADR-001: Test app fixture lives inside vertex-play

**Status:** Accepted
**Date:** 2026-04-10

## Context

Vertex-play is a CI/CD pipeline kit for building, signing, and publishing Android apps to Google Play. To validate the pipeline end-to-end across POC-A (build), POC-B (sign), and POC-C (upload), we need a real Android app as the input.

Constraints on the test app:

- **Play App Signing is one-time and irreversible per applicationId.** The test app must own its applicationId. We cannot enrol an app whose source belongs to someone else (e.g. `android/architecture-samples`).
- **Vertex-play is explicitly "not an app"** per `scope.md` and `repo-boundaries.md`. But the repo should be **shippable**: someone cloning vertex-play should have everything needed to validate the pipeline without external resources.
- **Real Android apps live in their own repos** (the future `vertex-app-*` pattern). The test fixture is the exception that proves the rule.

Three options were considered:

1. **Clone `android/architecture-templates`** — official Google starter, explicitly designed for cloning. Get up-to-date AGP/Compose/Kotlin scaffolding for free.
2. **Vibe-code or hand-roll a small app from scratch** (a todo app or similar — not literally hello-world).
3. **Hybrid:** clone the template as scaffolding, then extend with custom logic.

## Decision

**Option 2.** A small Android app will be written from scratch. It lives at `vertex-play/test-app/`.

## Consequences

- **Vertex-play remains "tooling, not an app"** at the conceptual level. `test-app/` is a fixture for validating the pipeline, not the project's deliverable.
- **Vertex-play stays shippable** — clone the repo, you have everything needed to run the pipeline end-to-end.
- **Real apps continue to live in their own repos.** `test-app/` is the only exception to the "vertex-play does not contain Android source" rule, and exists only because the pipeline cannot be validated without an input.
- **The test app's applicationId is owned by this project** and can be enrolled in Play App Signing without conflict.
- **`builder-image.md` currently uses `android/architecture-samples` as the POC-A validation target.** This will need updating to point at `test-app/` when E2 tickets are written. Flagged here so the discrepancy is not lost. Not fixed now (out of scope per ADR-004).
- **Small ongoing maintenance burden:** the test app's AGP/Kotlin/Compose versions need to be kept current as part of using the pipeline. Acceptable for a learning project.
