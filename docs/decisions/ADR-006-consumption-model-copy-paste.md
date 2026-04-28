# ADR-006: Consumption model — copy-paste reference Jenkinsfile

**Status:** Accepted
**Date:** 2026-04-28

## Context

vertex-play is pipeline tooling, not an app. Android app repos (e.g. a future `vertex-app-foo`) need a way to consume the pipeline definition. Three options were considered:

- **Copy-paste:** App repos copy `Jenkinsfile` from vertex-play's root and adapt it locally. vertex-play holds the canonical reference.
- **Jenkins Shared Library (`@Library`):** vertex-play exposes a shared library; app repos call into it with `@Library('vertex-play@main')`. Requires configuring the shared library in Jenkins and restructuring the Jenkinsfile into callable library form.
- **Point at vertex-play directly:** App repos reference vertex-play's `Jenkinsfile` without copying. Not a standard Jenkins multibranch pattern — each app repo needs its own `Jenkinsfile`.

## Decision

**Copy-paste the reference `Jenkinsfile`.**

App repos copy `vertex-play/Jenkinsfile` and adapt as needed. vertex-play's `Jenkinsfile` is the canonical source; intentional divergence per app is fine.

`@Library` ruled out for the lab context: it requires configuring the shared library in Jenkins, establishing a versioning discipline between vertex-play and each consuming app repo, and restructuring the Jenkinsfile into library callable form. That overhead does not pay off for 1–2 app repos.

## Consequences

- The reference `Jenkinsfile` at the root of vertex-play is the deliverable of E7 — it is what app repos copy
- App repos own their copy; they pick up vertex-play updates by manually syncing if they choose to
- `@Library` remains a clean future migration path if the number of app repos grows
- E7 does not need to configure Jenkins shared library infrastructure
