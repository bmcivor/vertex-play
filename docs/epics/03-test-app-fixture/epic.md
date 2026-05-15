---
type: epic
project: vertex-play
---

## E3 — Test app fixture

### Purpose

Create a minimal, buildable Android app at `vertex-play/test-app/` that the builder image (E2) can use for end-to-end validation. The test app exists solely to prove the pipeline works — it has no real functionality.

This app must own its own `applicationId` because Play App Signing enrollment is one-time and irreversible per `applicationId` (see ADR-001). Using an external project's app is not an option.

### In scope

- Android project structure at `test-app/` (Gradle wrapper, build files, manifest, minimal source)
- Version pins sourced from E2 ticket 006 (AGP, JDK target, compileSdk, targetSdk, build-tools)
- A vertex-play-owned `applicationId` (e.g. `com.vertex.play.testapp`)
- Enough code that `./gradlew :app:bundleRelease` succeeds and produces a valid `.aab`
- Local build verification on the dev machine before handing off to E2 009
- README in `test-app/` explaining what the app is and why it exists

### Out of scope

- Signing configuration (E5)
- Play Console registration or enrollment (E4)
- Any real app functionality, UI, or tests beyond what the build requires
- Running the build inside the builder image container (E2 009)
- Gradle wrapper version choice — use current stable, not gated by E2

### Dependencies

- E2 006 (version pins — AGP/SDK values feed into `build.gradle.kts`)

### Blocks

- E2 009 (end-to-end validation requires `test-app/` to exist)
- E5 (signing config applied to this app)
