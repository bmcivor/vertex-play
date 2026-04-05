# Building Blocks

Technical requirements for the Android build pipeline. Each row is a design decision and verification item — not assumed correct until validated in a POC.

| Area | Requirement | Why |
|------|-------------|-----|
| **JDK** | Version compatible with the target Android Gradle Plugin (AGP) | Wrong JDK breaks Kotlin/Gradle compilation. Pin in the builder Docker image. See [AGP/JDK compatibility](https://developer.android.com/build/releases/gradle-plugin#compatibility). |
| **Android SDK** | `cmdline-tools`, `platform-tools`, `build-tools` for target API, platform for target API. Optional: NDK if app uses native code. | CI must bake these into the builder image. License acceptance (`yes \| sdkmanager --licenses`) is required for unattended installs. |
| **Gradle** | Wrapper checked into the app repo (`gradlew` + `gradle/wrapper/`). Not installed in the builder image. | Reproducible builds. Jenkins calls `./gradlew`, not a system Gradle. |
| **Outputs** | Release AAB (Android App Bundle) for Play. Debug APK optional for artifacts. | Play requires AAB for new apps. `versionCode` must be monotonically increasing per `applicationId`. |
| **Signing** | Upload keystore enrolled in Play App Signing. | Keystore and passwords are secrets — Jenkins credentials only, never in git. Loss of upload key requires reset via Play Console. |
| **Versioning** | `versionCode` incremented per upload. | Derived from git tag, Jenkins build number, or explicit bump. Duplicate `versionCode` = upload rejection. |
| **Play API auth** | GCP project linked to Play Console. Service account with JSON key and Console permissions. | JSON key is a high-value secret. Minimum permission: release to internal testing track. |
| **Quality gates** | Lint, unit tests. Optional: instrumented tests, SAST. | Scope drives builder image size, job duration, and whether an emulator is needed. |
