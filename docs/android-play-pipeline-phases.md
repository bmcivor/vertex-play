Part of the hub [android-play-pipeline-plan.md](android-play-pipeline-plan.md). Task IDs, **Done** status, and **Recorded context** (including Australia) live there.

---

## Investigation phases (each produces written evidence)

**Phase I — App baseline (input from the real project)**

- Capture: min/target/compile SDK, AGP version, Kotlin version, product flavors, modules, presence of native code (NDK), and existing Gradle tasks (`bundleRelease`, `lint`, `test`).
- Outcome: a one-page **build contract** (commands that must succeed in CI).
- **Repo note:** Use a **minimal in-repo sample** or external template until `vertex-play` has a full app; **re-run Phase I** when modules/flavors change materially.

**Phase II — Google Play account and API**

- **First:** **II-00** — developer program enrollment and Console access (prerequisite if not yet enrolled). **II-00a** if in **Australia** — align account type and documents with Google’s AU list (ABN is not assumed for every signup; see **Recorded context** in the hub).
- Then confirm: organization access, ability to create/link **Google Cloud** project, enable **Google Play Android Developer API**, create **service account**, grant **Play Console** permissions (minimum for uploads to internal testing).
- Outcome: checklist with screenshots or internal notes (no secrets in repo)—and a test project/app for dry-run uploads.

**Phase III — Signing model**

- Confirm **Play App Signing** status for the app; locate **upload certificate** requirements.
- Outcome: documented **where** the upload keystore lives, **how** CI references it, and disaster recovery if keystore is lost (Google-held signing key vs upload key rotation process).

**Phase IV — Policy and listing readiness**

- Map app to: **Data safety form**, **target API policy**, **permissions justification**, **deletion/account requirements** if applicable.
- Outcome: **go/no-go** list before first production submission—this is what “verification” usually blocks on, not Gradle.

**Phase V — Lab capacity**

- Measure: disk for SDK images and Gradle caches, RAM for parallel jobs, CPU for compilation, feasibility of emulator if you want it.
- Cross-check Docker host notes in [docker-registry-investigation.md](../vertex-studio/docs/design/docker-registry-investigation.md) for **Docker 29 / insecure registry** behavior when pushing/pulling builder images.
