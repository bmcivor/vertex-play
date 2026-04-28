# E6 — Play upload tooling

**Status:** Tickets written.

## Purpose

Upload a signed AAB to Google Play's internal track using Gradle Play Publisher (GPP), wired into a throwaway Jenkins pipeline job. This is POC-C from `spec.md`.

The upload tool choice (GPP) is resolved in [ADR-005](../../decisions/ADR-005-upload-tool-gpp.md).

## In scope

- Add GPP plugin to test app's Gradle config
- Wire service account JSON credential (E4 016) into the upload task via env var
- Write a throwaway Jenkinsfile that signs and uploads
- Execute the upload and confirm the AAB appears in Play Console's internal track
- POC-C lab note

## Out of scope

- The reference app-pipeline `Jenkinsfile` (E7) — this epic uses a throwaway job, same discipline as POC-B
- Track promotion, release notes, rollouts — out of scope for the POC; GPP supports these for E7/E8
- Any new Jenkins credential infrastructure — the service account JSON from E4 016 is sufficient

## Dependencies

- E5 (signed AAB as pipeline input)
- E4 (fully complete — Play Console listing created, service account JSON in Jenkins)

## Blocks

- E7 (reference pipeline needs lessons from POC-C)

## Tickets

- [022 — Add GPP to test app Gradle config](tickets/022-add-gpp-to-test-app.md)
- [023 — Write throwaway Jenkinsfile for upload POC](tickets/023-write-throwaway-jenkinsfile-upload-poc.md)
- [024 — Trigger upload and verify AAB in Play Console](tickets/024-trigger-upload-and-verify.md)
- [025 — Write POC-C lab note](tickets/025-write-poc-c-lab-note.md)
