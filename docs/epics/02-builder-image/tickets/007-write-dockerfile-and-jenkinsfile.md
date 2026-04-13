## 7. Write Dockerfile and bootstrap Jenkinsfile

**Epic:** E2 — Builder image

**As a** pipeline developer
**I want** the builder image Dockerfile and its bootstrap Jenkinsfile committed on a working branch
**So that** Jenkins can build and push the Android builder image to the lab registry

### Context

Combines T2 (Dockerfile) and T3 (Jenkinsfile) from [`builder-image.md`](../../../implementation/builder-image.md). Both artefacts land on the same branch and are tightly coupled — the Jenkinsfile builds and pushes the image defined by the Dockerfile.

**Scope reminder:** the bootstrap Jenkinsfile builds vertex-play's own builder image. It is **not** the reference app-pipeline Jenkinsfile from spec.md section 2. Two different files, two different purposes.

### Acceptance criteria

#### Dockerfile (`docker/Dockerfile`)

- `FROM` uses the base image pin from G6
- Installs minimal system packages: `curl`, `unzip`, `ca-certificates`, `git`, `zip`, `file`
- Creates a non-root user for Gradle
- Downloads cmdline-tools at the G5 version, verifies SHA256
- Accepts licenses non-interactively (`yes | sdkmanager --licenses`)
- Installs `platform-tools`, `platforms;android-<G3>`, `build-tools;<G4>` — every version explicitly pinned
- Does **not** install Gradle (app repos provide their own wrapper)
- Smoke test as final build step: `java -version && sdkmanager --list | head -20 && echo OK`
- No `:latest` tags anywhere
- `docker build -t android-builder-local docker/` succeeds on a developer machine

#### Jenkinsfile (root)

- Matches the style (scripted vs declarative) used by vertex-block / vertex-studio
- Single stage: build and push the builder image
- Computes image tag per G7 convention
- `docker build` and `docker push` to `shadowlands:5000/android-builder:<tag>`
- No `:latest` tag — explicit pin or branch/SHA tag only
- Post-build: prints the pushed image digest

#### Both

- Committed on a working branch, not merged
- Branch name recorded for use in 008

### Dependencies

- 006 (version pins G1–G8 resolved)

### Blocks

- 008 (needs the branch pushed to trigger a Jenkins build)

### Priority

- High
