# ADR-005: Upload tool — Gradle Play Publisher (GPP)

**Status:** Accepted
**Date:** 2026-04-28

## Context

E6 requires a tool to upload a signed AAB to Google Play's internal track via the Play Developer API. Three candidates were evaluated:

- **Gradle Play Publisher (GPP)** (`com.github.triplet.play`) — Gradle plugin; upload is a Gradle task
- **fastlane** (`supply`) — Ruby-based mobile release management suite
- **Raw Play Developer API** — direct HTTP calls to Google's API

The upload tool choice was flagged in `epics.md` as a real uncertainty, to be resolved before E6 tickets were written.

## Decision

**Use Gradle Play Publisher (GPP).**

- No new runtime in the builder image — GPP is a Gradle plugin, declared in `build.gradle.kts` alongside the other plugins
- Authentication uses the service account JSON already stored in Jenkins from E4 016 — no new credential infrastructure
- Upload is a single Gradle task (`./gradlew publishBundle`), consistent with the rest of the build
- GPP supports all tracks, release notes, rollouts, and promotion — sufficient for the full reference pipeline in E7

fastlane ruled out: adds a Ruby runtime to the builder image for no capability gain over GPP. Raw API ruled out: requires writing and maintaining auth/upload logic that GPP already provides.

## Consequences

- GPP plugin version pin becomes part of the E6 Gradle config (ticket 022), following the same pinning discipline as the builder image (E2 006)
- Service account credential injected into the GPP extension via the env var pattern established in E5 — no new Jenkins credential required beyond E4 016
- GPP configuration lives in `test-app/app/build.gradle.kts`, consistent with the signing config added in E5 019
