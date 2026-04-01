Part of the hub [android-play-pipeline-plan.md](android-play-pipeline-plan.md). Task IDs and **Done** status live there.

---

## Technical building blocks (inventory to validate, not assume)


| Area              | What must be true                                                                                  | Why it matters for your lab                                                               |
| ----------------- | -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **JDK**           | Version compatible with your **Android Gradle Plugin (AGP)**                                       | Wrong JDK breaks Kotlin/Gradle; pin in Docker image or toolchains                         |
| **Android SDK**   | `cmdline-tools`, platforms, build-tools, (optional) **NDK**                                        | CI must install or bake these; license acceptance is required for unattended installs     |
| **Gradle**        | Prefer **Gradle Wrapper** in the app repo                                                          | Reproducible builds; Jenkins calls `./gradlew`                                            |
| **Outputs**       | **Release AAB** for Play; debug APK/AAB optional for artifacts                                     | Play upload path targets AAB + versionCode rules                                          |
| **Signing**       | **Upload key** (and Play App Signing enrollment)                                                   | Keystore/passwords are secrets—Jenkins credentials or external secret store; never in git |
| **Versioning**    | **versionCode** monotonic per package name                                                         | CI must bump or derive (tags, pipeline params) or uploads fail                            |
| **Play API auth** | **Google Cloud** project linked to Play Console; **service account** with Play Console permissions | JSON key is a high-value secret                                                           |
| **Quality gates** | Lint, unit tests, (optional) UI/instrumented, SAST                                                 | Scope drives agent hardware and job time                                                  |


Nothing in the list above should be “assumed correct”—each row is a **design decision + verification** item (see POCs in [android-play-pipeline-pocs.md](android-play-pipeline-pocs.md)).
