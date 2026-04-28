# Vertex-play epics

Planning index for vertex-play. Derived from [`spec.md`](spec.md) and shaped by the workflow defined in [ADR-002](decisions/ADR-002-docs-structure.md).

Each epic is a unit of work that delivers a meaningful slice of the pipeline. Tickets live under `epics/<NN>-<slug>/tickets/` and are globally numbered across the whole project. POC lab notes land in [`pocs/`](pocs/).

## Status

- Ordering: locked
- Epic files: drafted incrementally as each epic becomes the next ticket-breakdown target
- Tickets: E1 written (001–004); E2 written (005–010); E3 written (011–012); E4 written (013–016); E5 written (017–021); E6 written (022–025); E7 written (026–028); E8 written (029–031)
- POCs: not yet executed

## Order

E4 (Google Play developer enrollment) is a long manual tail that runs in parallel with everything else. E3 (test app fixture) runs in parallel with E1/E2 and must exist before E2's validation step completes.

```
E4 ───────────────────────────────────────────────────────────────►
E1 → E2 ────────────→ E5 ────→ E6 ────→ E7 ────→ E8
     E3 (parallel)
```

## Epics

### E1 — Lab integration and Jenkins onboarding

Add vertex-play to vertex-studio's `jenkins_repos` Ansible inventory, redeploy Jenkins, confirm the repo is indexed and discoverable as a multibranch project. Mostly cross-repo work in vertex-studio. Small epic, but unblocks Jenkins-triggered builds for everything that follows.

**Directory:** [`epics/01-lab-integration/`](epics/01-lab-integration/epic.md)

### E2 — Builder image

The Android builder Docker image: `docker/Dockerfile` with pinned JDK / SDK / cmdline-tools / build-tools, plus a bootstrap `Jenkinsfile` that builds and pushes the image to `shadowlands:5000`. This is POC-A from `spec.md`. The existing plan in [`implementation/builder-image.md`](implementation/builder-image.md) serves as the POC plan for this epic (see [ADR-004](decisions/ADR-004-builder-image-md-retained.md)).

**Directory:** [`epics/02-builder-image/`](epics/02-builder-image/epic.md)

**Depends on:** E1 (for Jenkins-triggered build); E3 (for end-to-end validation)

### E3 — Test app fixture

A small Android app at `vertex-play/test-app/`, used as the validation target across POC-A / POC-B / POC-C. Owned by this project so its `applicationId` can be enrolled in Play App Signing. See [ADR-001](decisions/ADR-001-test-app-fixture.md) for rationale.

**Directory:** [`epics/03-test-app-fixture/`](epics/03-test-app-fixture/epic.md)

**Depends on:** E2 006 (version pins)

### E4 — Google Play developer enrollment and API access

Manual long-tail work: Play Console signup, identity verification, GCP project, Play Developer API enabled, service account JSON key, scoped permissions in Play Console. Runs in parallel with everything else. Blocks the Play App Signing step in E5 and all of E6.

**Directory:** [`epics/04-play-enrollment/`](epics/04-play-enrollment/epic.md)

**Depends on:** E3 011 (for `applicationId` in the Play Console listing); E1 003 (for Jenkins credential storage). Core manual work (013) depends on nothing technical.

### E5 — Signing

Upload keystore generation/storage, Jenkins credentials wiring, Gradle signing config in the test app, signed AAB produced inside the builder image. POC-B from `spec.md`. Play App Signing is auto-enrolled via E4 015 (new apps auto-enrol since Aug 2021), so no separate enrolment ticket is required.

**Directory:** [`epics/05-signing/`](epics/05-signing/epic.md)

**Depends on:** E2 (builder image), E3 012 (test app buildable), E4 015 (Play Console listing)

### E6 — Play upload tooling

Decide the upload tool (Gradle Play Publisher, fastlane, or raw Play Developer API), wire it into the pipeline, upload a signed AAB to an internal track. POC-C from `spec.md`. The upload tool choice is a real uncertainty that resolves through the POC, not pre-baked in tickets.

**Directory:** [`epics/06-play-upload/`](epics/06-play-upload/epic.md)

**Depends on:** E5, E4 (fully complete)

### E7 — Reference app pipeline

The reference `Jenkinsfile` that real app repos consume — distinct from the bootstrap `Jenkinsfile` in E2. Includes the branch/promotion model from `spec.md` (PR / main / tag), `versionCode` derivation, and the open question of how an app repo actually consumes vertex-play (copy-paste, Jenkins shared library, `@Library`). The consumption-model question is the second real uncertainty in the project and likely needs its own POC.

**Directory:** [`epics/07-reference-pipeline/`](epics/07-reference-pipeline/epic.md)

**Depends on:** Lessons from E2, E5, E6

### E8 — Handoff and documentation

Short "how to adopt vertex-play in an app repo" doc, updates to `README.md` and `spec.md` if any pre-project assumptions turned out wrong, lessons learned. Likely also where an eventual mkdocs setup for vertex-play lives, matching vertex-studio's setup.

**Directory:** [`epics/08-handoff/`](epics/08-handoff/epic.md)

**Depends on:** Everything else
