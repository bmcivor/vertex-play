## 23. Write throwaway Jenkinsfile for upload POC

**Epic:** E6 — Play upload tooling

**As a** pipeline developer
**I want** a throwaway Jenkinsfile that signs and uploads the test app AAB
**So that** POC-C can be triggered from Jenkins without manual steps

### Context

Same discipline as E5 020: a throwaway pipeline script or minimal `Jenkinsfile`, **not** the reference app-pipeline `Jenkinsfile` (E7). It can live in a scratch branch or as an inline pipeline script in a throwaway Jenkins job — whichever is easier to clean up afterwards.

The pipeline:

1. Uses the builder image from `shadowlands:5000` as agent
2. Checks out `test-app/`
3. Injects the signing credentials from E5 018 (same as E5 020)
4. Injects the Play service account JSON from E4 016 as `PLAY_SERVICE_ACCOUNT_JSON`
5. Runs `./gradlew :app:publishBundle --no-daemon --stacktrace`

Signing and upload can be one stage or two — keep it simple.

### Acceptance criteria

- Jenkinsfile (or inline pipeline script) written
- Uses builder image from `shadowlands:5000` as agent (or `docker run` equivalent)
- Injects signing credentials (E5 018 credential IDs) and service account JSON (E4 016 credential ID) via `withCredentials`
- Runs `./gradlew :app:publishBundle --no-daemon --stacktrace`
- No credentials appear in the Jenkinsfile or pipeline script in plaintext — all via `withCredentials`
- Credential IDs match what was recorded in E5 018 and E4 016

### Notes

- The Jenkinsfile does not need to be merged to `main` — a scratch branch or inline script is fine for the POC
- Record which approach was used (branch vs inline script) in the POC-C lab note (025)

### Dependencies

- 022 (GPP task name and env var name confirmed)
- E5 018 (signing credential IDs)
- E4 016 (service account credential ID)
- E2 008 (builder image in registry)

### Blocks

- 024 (execution)

### Priority

- Medium
