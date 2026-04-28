## 24. Trigger upload and verify AAB in Play Console (POC-C)

**Epic:** E6 — Play upload tooling

**As a** pipeline developer
**I want** a signed AAB successfully uploaded to the Play Console internal track
**So that** POC-C is validated end-to-end

### Context

Run the pipeline from 023 and confirm the upload lands. Success is seeing the AAB in Play Console's internal track — not just a green Jenkins build. The Play Console UI (or the GPP task output in the build log) is the confirmation.

### Acceptance criteria

- Jenkins pipeline from 023 runs to completion (green build)
- GPP task output in build log shows upload success (no error from GPP)
- AAB appears in Play Console → vertex-play app → Internal testing track
- Version code of the uploaded build recorded
- No secrets (service account JSON content, keystore passwords) visible in Jenkins build logs
- SHA256 of the uploaded AAB recorded

### Failure modes

- **`applicationId` mismatch:** GPP derives `applicationId` from the app's `build.gradle.kts` — must match the Play Console listing created in E4 015
- **Service account lacks permission:** service account needs at least `Release to internal testing track` in Play Console — verify permissions match E4 014's setup
- **Play App Signing not enrolled:** should be auto-enrolled via E4 015; if Play Console shows a signing error, check Play App Signing status before re-running
- **`packageName` not found in Play Console:** the app listing (E4 015) must exist and be in a state that accepts internal track uploads (listing may need to be in a published or draft state — check Play Console requirements)

### Dependencies

- 023 (Jenkinsfile ready)
- E4 016 (service account in Jenkins)
- E5 020 (signed AAB buildable — pipeline in 023 re-runs the build and signs)

### Blocks

- 025 (lab note)

### Priority

- High
