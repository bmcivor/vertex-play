Part of the hub [android-play-pipeline-plan.md](android-play-pipeline-plan.md). Task IDs and **Done** status live there.

---

## POC sequence (recommended order)

1. **POC-A — Headless `bundleRelease` in Docker**: Dockerfile with JDK + SDK + accepted licenses; run `./gradlew :app:bundleRelease` for a **minimal sample app** or your real app in a branch. Proves toolchain and reproducibility on the lab.
2. **POC-B — Signing**: Configure signing config wired from **environment/credentials** (not committed); verify `jarsigner` / Play’s signing requirements on the produced AAB.
3. **POC-C — Internal track upload**: Using a **throwaway** Play app, upload the AAB with chosen tool (GPP/fastlane/API); confirm versionCode handling and track visibility.
4. **POC-D (optional) — Instrumented tests**: Only if Phase IV demands it; spike emulator or external lab from Jenkins and record stability and duration.

Each POC should end with a short **lab note** under `docs/design/` (you can adopt the tone of the existing registry investigation doc)—what worked, exact versions, and failures.

---

## Deliverables when “design is fully fleshed out”

1. **Target architecture diagram** (repos, Jenkins, registry image, secrets flow, Play API).
2. **Stage definitions** for the `Jenkinsfile` (or shared library) with explicit inputs/outputs per stage.
3. **Secret inventory** (keystore, Play JSON, any API keys) with storage location and rotation owners.
4. **Play Console checklist** (manual) aligned with your app category.
5. **Risk register**: e.g. emulator omitted, single-machine Jenkins SPOF, review rejection paths.

---

## What this plan explicitly does not decide yet

- Final scope of a dedicated `**vertex-play`** repo (**lab CI tooling**, **product app**, or **hybrid**)—see **Phase P** todos (name can still change).
- Single-app vs monorepo, Kotlin Multiplatform, or Flutter/React Native (each changes Gradle/plugins and CI).
- Whether instrumented tests are in scope for v1.
- Whether you require **compliance scanning** (SBOM, dependency policies) as gates.

Those should be resolved during **Phase P**, Phase I, and the POC gates—not assumed at planning time.
