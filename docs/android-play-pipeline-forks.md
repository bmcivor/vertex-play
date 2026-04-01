Part of the hub [android-play-pipeline-plan.md](android-play-pipeline-plan.md). Task IDs and **Done** status live there.

---

## Major design forks (choose explicitly during investigation)

These change architecture cost and feasibility on a home lab:

1. **Where the Android toolchain runs**
  - **A. Dockerized build** (Jenkins runs `docker build` / `docker run` with an image that contains SDK + pinned JDK): aligns with your existing socket-based Jenkins; image can be stored in `**shadowlands:5000`** after POC.
  - **B. SDK installed on lab host**: simpler for one-off experiments, worse for reproducibility and upgrades.
2. **Instrumented / UI tests in CI**
  - **None (initial)**: only JVM unit tests + lint + assemble—lowest friction.
  - **Emulator on lab**: needs **KVM**/CPU features, RAM, and stable headless emulator images; often painful on heterogeneous hardware.
  - **External farm** (e.g. Firebase Test Lab, proprietary device clouds): extra accounts, cost, network egress from Jenkins—document egress from Tailscale lab to Google APIs.
3. **Upload automation**
  - **Gradle Play Publisher** vs **fastlane** vs **raw API**: pick based on team familiarity and whether the app is single-module or complex flavor/matrix.
4. **Branch / promotion model**
  - e.g. PR builds → debug; `main` → internal track; tagged release → closed/production—must match your Play track strategy and signing keys.
