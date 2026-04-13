## 6. Resolve version pins (G1–G8)

**Epic:** E2 — Builder image

**As a** pipeline developer
**I want** every version used in the builder image resolved to an explicit pin before writing the Dockerfile
**So that** the image is reproducible and no version is left to float or default to latest

### Context

This ticket executes gates G1–G8 from [`builder-image.md`](../../../implementation/builder-image.md). ADR-001 changed the validation target from `architecture-samples` to `test-app/`, which doesn't exist yet (E3). Instead of reading versions from an existing project, this ticket **chooses** current stable versions from the AGP/JDK compatibility matrix and Android SDK release channels. E3 writes the test app to match these choices.

### Acceptance criteria

- G1 resolved: AGP version chosen (current stable), recorded with date checked
- G2 resolved: JDK version derived from G1 via the AGP/JDK compatibility table, explicit Temurin version chosen
- G3 resolved: `compileSdk` and `targetSdk` API levels chosen
- G4 resolved: build-tools version pinned (latest stable compatible with G3)
- G5 resolved: cmdline-tools version pinned with download URL and SHA256
- G6 resolved: base image `FROM` reference — explicit tag at minimum, digest preferred
- G7 resolved: image tag scheme for `shadowlands:5000` matches existing lab convention (inspected from vertex-block / vertex-studio Jenkinsfiles)
- G8 resolved: confirmed whether the bootstrap Jenkinsfile needs the `docker-workflow` plugin (inspect existing lab Jenkinsfiles)
- All values recorded in a single location (working notes or draft lab note section) for use in 007

### Dependencies

- 005 (lab prerequisites green)

### Blocks

- 007 (Dockerfile and Jenkinsfile use these values)
- E3 (test app should use the AGP/SDK versions chosen here)

### Priority

- High
