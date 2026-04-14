# E5 — Signing

**Status:** Tickets written.

## Purpose

Produce a signed release AAB for the test app, inside the builder image, using credentials stored in Jenkins — never in git. This is POC-B from `spec.md`.

The upload keystore is generated locally and moved to Jenkins credentials. The test app's Gradle build reads keystore path and passwords from environment variables that Jenkins injects. Play App Signing is already in effect because ticket 015 created the app listing (new apps auto-enrol since August 2021), so Google holds the app signing key and we hold the upload key.

## In scope

- Generate upload keystore with `keytool`
- Store keystore and passwords in Jenkins credentials
- Add Gradle signing config to `test-app/app/build.gradle.kts` (reads from env vars)
- Produce a signed AAB end-to-end using the builder image and Jenkins-injected credentials
- POC-B lab note

## Out of scope

- The reference app-pipeline `Jenkinsfile` (E7) — signing validation here can use a throwaway job or a minimal pipeline script
- Play upload (E6) — this epic stops at "signed AAB produced"
- Play App Signing enrolment UX — auto-enrolled via E4 015; no action required

## Dependencies

- E2 (complete — need the builder image in the registry to sign inside)
- E3 012 (test app builds locally — so signing config can be added)
- E4 015 (Play Console listing — Play App Signing auto-enrolled, avoids surprises at E6 upload time)

**Not** dependent on E4 014 or 016 — signing doesn't use the Play Developer API.

## Blocks

- E6 (upload tooling needs a signed AAB as input)

## Tickets

- [017 — Generate upload keystore](tickets/017-generate-upload-keystore.md)
- [018 — Store keystore and passwords in Jenkins credentials](tickets/018-store-keystore-in-jenkins.md)
- [019 — Add Gradle signing config to test app](tickets/019-gradle-signing-config.md)
- [020 — End-to-end signing validation (POC-B)](tickets/020-end-to-end-signing-validation.md)
- [021 — Write POC-B lab note](tickets/021-write-poc-b-lab-note.md)
