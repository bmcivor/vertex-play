## 12. Verify local build produces release AAB

**Epic:** E3 — Test app fixture

**As a** pipeline developer
**I want** to confirm the test app builds a valid release AAB on my dev machine
**So that** E2 009 (container validation) can proceed with confidence that the app itself is sound

### Context

This is the local equivalent of E2 ticket 009's container build. If the app doesn't build locally, it won't build in the container either. Running this on the dev machine first isolates app-level issues from container-level issues.

The checks here deliberately mirror what 009 will run, so a pass here means 009 failures are container-specific (JDK mismatch, missing SDK component, etc.) rather than app-level.

### Acceptance criteria

- `cd test-app && ./gradlew :app:bundleRelease --no-daemon --stacktrace` completes successfully
- Output `.aab` located (expected at `app/build/outputs/bundle/release/app-release.aab`)
- Sanity check: `unzip -l <aab>` shows `BundleConfig.pb` and `base/manifest/AndroidManifest.xml`
- SHA256 of the `.aab` recorded (for comparison if 009 produces a different hash — expected, since unsigned builds aren't reproducible, but useful for debugging)
- JDK version used for the build recorded (to compare against E2 006 G2)

### Failure modes

- **Gradle wrapper download fails:** network or proxy issue on dev machine — not an app problem, retry or check connectivity
- **AGP/Gradle version incompatibility:** wrong Gradle wrapper version for the AGP pin from E2 006 — update `gradle-wrapper.properties` to a compatible version (check the [AGP release notes](https://developer.android.com/build/releases/gradle-plugin) for the compatibility table)
- **compileSdk not installed locally:** `sdkmanager` on dev machine may not have the platform — install it locally or note that this is expected to work only in the builder image (but local verification is preferred)
- **Missing `android.useAndroidX` or similar property:** add to `gradle.properties` in 011

### Dependencies

- 011 (test app project must exist)

### Blocks

- E2 009 (end-to-end validation — app is confirmed buildable)

### Priority

- Medium
