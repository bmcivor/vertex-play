## 9. End-to-end validation: build test app inside image

**Epic:** E2 — Builder image

**As a** pipeline developer
**I want** to build the test app inside the builder image and produce an unsigned AAB
**So that** the image is proven to work for a real Android build, not just smoke tests

### Context

Maps to T7 from [`builder-image.md`](../../../implementation/builder-image.md), adapted per ADR-001: uses `test-app/` from E3 instead of `android/architecture-samples`.

### Acceptance criteria

- Pull the image from `shadowlands:5000` (the image pushed in 008, not a local build)
- Run a container from the image with `test-app/` available (bind-mount or clone)
- `./gradlew :app:bundleRelease --no-daemon --stacktrace` completes successfully
- The produced `.aab` is located (exact path confirmed at execution)
- Sanity check: `unzip -l <aab>` lists `BundleConfig.pb` and `base/manifest/AndroidManifest.xml`
- SHA256 of the `.aab` is recorded
- Document whether validation ran on a dev machine or on shadowlands

### Failure modes

- "Incompatible JDK" → G2 was wrong, revisit 006
- "SDK component not installed" → G3/G4/G5 missed something, revisit 007
- License prompt during Gradle → sdkmanager license acceptance incomplete, revisit 007
- Out of disk in container → bind-mount a larger scratch volume

### Dependencies

- 008 (image exists in registry)
- E3 (test app must exist at `test-app/`)

### Blocks

- 010 (lab note captures these results)
- E5 (signing builds on confirmed builder image)

### Priority

- High
