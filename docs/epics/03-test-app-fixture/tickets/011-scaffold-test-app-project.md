## 11. Scaffold test app project

**Epic:** E3 — Test app fixture

**As a** pipeline developer
**I want** a minimal Android app project at `test-app/` with version pins matching E2 006
**So that** the builder image has a real, vertex-play-owned app to validate against

### Context

This app exists solely for pipeline validation — it is the exception to the "no Android source in vertex-play" rule (see [ADR-001](../../../../decisions/ADR-001-test-app-fixture.md)). It must own its own `applicationId` because Play App Signing enrollment is one-time and irreversible per ID.

The AGP, compileSdk, targetSdk, and build-tools versions are not chosen here — they come from E2 ticket 006. This ticket consumes those values.

### Acceptance criteria

#### Project structure (`test-app/`)

- `settings.gradle.kts` — declares the `:app` module
- `build.gradle.kts` (root) — applies the Android Gradle Plugin at the version from E2 006, does not apply it to the root project itself
- `app/build.gradle.kts` — applies `com.android.application`, sets `compileSdk`, `targetSdk`, `minSdk`, `versionCode`, `versionName`, and `applicationId`
- `gradle.properties` — standard Android/Gradle properties (e.g. `android.useAndroidX=true`, `org.gradle.jvmargs`)
- Gradle wrapper checked in: `gradlew`, `gradlew.bat`, `gradle/wrapper/gradle-wrapper.jar`, `gradle/wrapper/gradle-wrapper.properties` — use current stable Gradle (not gated by E2)

#### Version pins

- AGP version matches E2 006 (G1)
- `compileSdk` and `targetSdk` match E2 006 (G3)
- `build-tools` version matches E2 006 (G4)
- `minSdk` set to a reasonable floor (e.g. 24) — not gated by E2, just needs to be at or below `targetSdk`

#### App source

- `applicationId` is vertex-play-owned (e.g. `com.vertex.play.testapp`) — record the chosen value for E4/E5
- `app/src/main/AndroidManifest.xml` — declares the application; includes a launcher activity so the app is technically installable (some build tools warn without one)
- Minimal activity class (Kotlin or Java, matching the AGP/project convention) — empty `onCreate` is fine
- No real UI, no tests, no resources beyond what the build requires

#### Documentation

- `test-app/README.md` explaining: what the app is, why it exists (ADR-001), where version pins come from (E2 006), that it has no functional purpose

#### Commit

- Committed on a working branch, not merged
- No files outside `test-app/` modified

### Dependencies

- E2 006 (version pins — AGP, compileSdk, targetSdk, build-tools values must be resolved first)

### Blocks

- 012 (local build verification)

### Priority

- Medium
