## 8. Trigger Jenkins build and verify image in registry

**Epic:** E2 — Builder image

**As a** pipeline developer
**I want** Jenkins to build and push the builder image from the working branch
**So that** the image exists in the lab registry and was produced by CI, not a developer laptop

### Context

Maps to T6 from [`builder-image.md`](../../../implementation/builder-image.md). This is the first time vertex-play's Jenkinsfile runs in the lab.

### Acceptance criteria

- A build is triggered on the 007 branch via the Jenkins UI (or webhook if configured)
- The build log shows `docker build` and `docker push` completing successfully
- `curl http://shadowlands:5000/v2/android-builder/tags/list` shows the G7 tag
- The image digest is recorded from the push output
- Existing projects (vertex-block, vertex-studio) are unaffected

### Failure modes

- Build succeeds locally (007) but fails in Jenkins → difference in Docker daemon, storage driver, or network. Capture the build log and diagnose.
- Push fails with "server gave HTTP response to HTTPS client" → insecure-registries misconfigured. Should have been caught in E1 001/004.

### Dependencies

- 007 (Dockerfile and Jenkinsfile committed and pushed)
- E1 003 (vertex-play visible in Jenkins)

### Blocks

- 009 (needs a pushed image to validate against)

### Priority

- High
