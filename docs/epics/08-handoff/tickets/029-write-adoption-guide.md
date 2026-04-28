## 29. Write adoption guide

**Epic:** E8 — Handoff and documentation

**As a** pipeline developer
**I want** a concise adoption guide for vertex-play
**So that** an app developer can onboard a new Android app onto the pipeline without archaeology

### Context

This is the primary handoff deliverable. The audience is a developer who owns an Android app repo and wants to wire it into the vertex-play pipeline. They are assumed to have access to the lab (Jenkins, registry) but may not have followed this project's planning.

File lives at `docs/adoption.md`.

### Acceptance criteria

- File `docs/adoption.md` created
- Covers the following steps in order:
  1. **Prerequisites** — what must already exist: Jenkins running, builder image in registry, Play Console account and service account set up, app enrolled in Play App Signing
  2. **Copy the Jenkinsfile** — copy `Jenkinsfile` from vertex-play root into the app repo; list the places that need customising (credential IDs, `applicationId`, any app-specific Gradle task names)
  3. **Jenkins credentials** — the four credential IDs the pipeline expects (upload keystore file, keystore password, key password, Play service account JSON); where to create them in Jenkins; what type each one is (secret file vs secret text)
  4. **Configure GPP** — add GPP to the app's `build.gradle.kts`; the `play` extension config; the `PLAY_SERVICE_ACCOUNT_JSON` env var pattern
  5. **`versionCode`** — add `BUILD_NUMBER` reading to `build.gradle.kts`
  6. **Add to Jenkins** — add the app repo to `jenkins_repos` in vertex-studio inventory and redeploy (or point to the existing vertex-studio playbook)
  7. **Verify** — what a successful first build on each trigger (PR / main / tag) looks like
- No secrets or credential values in the guide — credential IDs only
- References the E7 lab note (`docs/pocs/e7-reference-pipeline.md`) for pipeline design detail
- Concise — this is a how-to, not a tutorial; skip rationale that belongs in ADRs

### Dependencies

- E7 028 (E7 lab note — adoption guide references it)
- All POC lab notes (A/B/C) complete — adoption guide can reference them for troubleshooting context

### Priority

- High
