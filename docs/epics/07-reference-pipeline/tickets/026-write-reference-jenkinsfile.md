## 26. Write reference Jenkinsfile

**Epic:** E7 — Reference app pipeline

**As a** pipeline developer
**I want** a reference `Jenkinsfile` at the root of vertex-play
**So that** Android app repos have a working, copy-and-adapt pipeline definition

### Context

This is the primary deliverable of vertex-play. It synthesises the throwaway jobs from E5 020 (signing) and E6 023 (upload) into a single coherent pipeline. The POC jobs can be used as the starting point.

The pipeline uses the branch/promotion model from `spec.md`:

| Trigger | Stages run |
|---------|-----------|
| Pull request | Build, Test |
| Merge to `main` | Build, Test, Sign |
| Git tag | Build, Test, Sign, Upload |

`versionCode` is derived from Jenkins `BUILD_NUMBER` and injected into the Gradle build as an env var. The app's `build.gradle.kts` reads it at build time (e.g. `versionCode = System.getenv("BUILD_NUMBER")?.toIntOrNull() ?: 1`).

**Stage ordering note from spec.md:** Quality (lint + test) runs before Sign and Upload. A test failure stops the pipeline before signing or uploading.

### Acceptance criteria

- `Jenkinsfile` exists at the root of vertex-play
- Agent: builder image from `shadowlands:5000` (via `agent { docker { image '...' } }`)
- Stages: Build, Test, Sign, Upload — each as a named stage
- Sign stage runs only on `main` branch or git tag (`when` condition)
- Upload stage runs only on git tag (`when` condition)
- All credentials injected via `withCredentials` — signing credentials from E5 018, service account JSON from E4 016
- `BUILD_NUMBER` passed to Gradle as `-PversionCode=$BUILD_NUMBER` or equivalent
- No credentials or secrets in the Jenkinsfile in plaintext
- Pipeline fails fast: any stage failure stops subsequent stages
- Jenkinsfile is annotated with comments indicating where an app repo would customise (e.g. `applicationId`, credential IDs)

### Notes

- The `when` conditions for branch detection in a Jenkins multibranch pipeline use `branch 'main'` and `buildingTag()` — verify these work with the lab's Jenkins version before finalising
- Keep the Jenkinsfile readable: a real app developer will be copying and adapting this

### Dependencies

- E5 020 (signing POC Jenkinsfile as reference)
- E6 023 (upload POC Jenkinsfile as reference)
- E2 008 (builder image tag confirmed)

### Blocks

- 027 (validation needs the Jenkinsfile)

### Priority

- High
