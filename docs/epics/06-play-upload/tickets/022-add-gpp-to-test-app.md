## 22. Add GPP to test app Gradle config

**Epic:** E6 — Play upload tooling

**As a** pipeline developer
**I want** Gradle Play Publisher configured in the test app
**So that** the upload task is available to the Jenkins pipeline in 023

### Context

GPP is a Gradle plugin (`com.github.triplet.play`). Adding it involves:

1. Declaring the plugin in `test-app/app/build.gradle.kts` with a pinned version
2. Configuring the `play` extension: service account JSON path (from an env var, not hardcoded), default track (`internal`), artifact sourced from `bundleRelease`

The service account JSON path follows the same env-var injection pattern as the signing credentials in E5 019 — the Jenkinsfile (023) binds the Jenkins secret file to a named env var, Gradle reads it at execution time.

Pin the GPP plugin version to a specific stable release. Check `plugins.gradle.org/plugin/com.github.triplet.play` for the current stable at time of execution — do not use `latest.release`.

### Acceptance criteria

- GPP plugin declared with a pinned version in `test-app/app/build.gradle.kts`
- `play` extension configured:
  - `serviceAccountCredentials.set(file(System.getenv("PLAY_SERVICE_ACCOUNT_JSON")))` (or equivalent Kotlin DSL form)
  - `track.set("internal")`
  - Artifact sourced from `bundleRelease` (GPP's default when the AGP plugin is present — confirm or set explicitly)
- `./gradlew tasks | grep -i publish` shows GPP tasks (e.g. `publishBundle`)
- App still builds cleanly without `PLAY_SERVICE_ACCOUNT_JSON` set — Gradle configure phase must not fail if the env var is absent; GPP should error only at task execution time

### Failure modes

- **Configure-time failure when env var absent:** wrap the env var read so it resolves to a placeholder path at configure time — GPP will fail at execution, not at sync
- **Plugin version not found:** verify the exact version string against the plugin portal before committing

### Dependencies

- 019 (signing config already in `build.gradle.kts` — add GPP config alongside it)
- E4 016 (credential exists in Jenkins — needed in 023, but worth knowing the credential ID now so the env var name can be chosen consistently)

### Blocks

- 023 (Jenkinsfile references the GPP task name)

### Priority

- Medium
