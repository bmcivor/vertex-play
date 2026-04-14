## 20. End-to-end signing validation (POC-B)

**Epic:** E5 — Signing

**As a** pipeline developer
**I want** a signed release AAB produced by the builder image using Jenkins-injected credentials
**So that** POC-B is validated before committing to a full reference Jenkinsfile in E7

### Context

This is the POC-B proof: Jenkins can sign. The vehicle can be a throwaway pipeline job or a minimal `Jenkinsfile` script — **not** the reference app-pipeline `Jenkinsfile`, which is E7's territory. Keep it scrappy; lessons learned here inform E7's design.

The pipeline runs the builder image from E2 as its agent (or via `docker run`), binds or clones `test-app/`, injects the three credentials from 018 as env vars, and runs `./gradlew :app:bundleRelease`. The output AAB must verify as signed with the upload key.

### Acceptance criteria

- A Jenkins pipeline (job or pipeline script) exists that:
  - Uses the builder image from `shadowlands:5000` as agent or run target
  - Checks out or makes `test-app/` available inside the container
  - Injects the three credentials from 018 via `withCredentials`, binding them to the env var names from 019 (e.g. `UPLOAD_KEYSTORE_FILE`, `UPLOAD_KEYSTORE_PASSWORD`, `UPLOAD_KEY_PASSWORD`)
  - Runs `./gradlew :app:bundleRelease --no-daemon --stacktrace`
- Pipeline succeeds
- Output AAB exists at the expected path
- `jarsigner -verify -verbose -certs <aab>` reports `jar verified`
- Certificate details in the verification output match the upload keystore (check subject DN, validity dates)
- SHA256 of the signed AAB recorded
- No keystore password or path appears in Jenkins build logs — console output does not leak secrets

### Failure modes

- **`keystore was tampered with, or password was incorrect`:** wrong password env var wiring — double-check 018's credential IDs and 019's env var names
- **`No signing config found`:** env vars not injected to the container, or signing config gracefully degraded per 019 — check `withCredentials` block and Gradle config
- **`jarsigner: unable to open jar file`:** AAB path wrong
- **Pipeline job is not a throwaway:** decide what to do with it after POC — delete or keep as reference for E7

### Dependencies

- 018 (credentials in Jenkins)
- 019 (signing config in build.gradle.kts)
- E2 008 (image in registry)

### Blocks

- 021 (lab note needs the POC results)
- E6 (upload needs a signed AAB)

### Priority

- High
