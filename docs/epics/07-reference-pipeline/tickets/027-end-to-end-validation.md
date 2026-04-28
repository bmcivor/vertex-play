## 27. End-to-end validation of all three triggers

**Epic:** E7 — Reference app pipeline

**As a** pipeline developer
**I want** all three branch triggers validated against the test app
**So that** the reference Jenkinsfile is confirmed working before it is handed off

### Context

Three scenarios must pass, exercised in order (each builds confidence for the next):

1. **Pull request** — only Build and Test stages run; Sign and Upload are skipped
2. **Merge to `main`** — Build, Test, and Sign run; Upload is skipped; signed AAB produced
3. **Git tag** — all stages run; signed AAB uploaded to Play Console internal track

The test-app from E3 is the build target. The multibranch pipeline for vertex-play in Jenkins (set up in E1) is the job that runs these.

### Acceptance criteria

**Scenario 1 — Pull request:**
- Create a branch, open a PR (or simulate a branch build in Jenkins)
- Build and Test stages complete successfully
- Sign and Upload stages do not run (confirmed in build log)
- No credentials injected unnecessarily

**Scenario 2 — Merge to `main`:**
- Push to `main` triggers a build
- Build, Test, and Sign stages complete
- Upload stage does not run
- Signed AAB produced at expected path
- `jarsigner -verify` confirms AAB is signed

**Scenario 3 — Git tag:**
- Push a git tag (e.g. `v0.1.0`) to trigger the pipeline
- All four stages complete
- AAB uploaded to Play Console internal track
- AAB visible in Play Console
- `versionCode` in the uploaded build matches `BUILD_NUMBER` from the Jenkins build log

### Failure modes

- **`when` condition mismatch:** branch detection or `buildingTag()` not working as expected — inspect `env.BRANCH_NAME` and `env.TAG_NAME` in the build log to diagnose
- **Sign stage runs on PR:** `when` condition too broad — tighten the branch match
- **Upload runs on `main` push:** tag detection condition too loose — verify `buildingTag()` vs `tag` pattern
- **`versionCode` not incrementing:** confirm `BUILD_NUMBER` is injected and read correctly in `build.gradle.kts`

### Dependencies

- 026 (reference Jenkinsfile written)
- E1 003 (vertex-play multibranch pipeline in Jenkins)
- E5 018 (signing credentials in Jenkins)
- E4 016 (service account credential in Jenkins)
- E2 008 (builder image in registry)

### Blocks

- 028 (lab note)

### Priority

- High
