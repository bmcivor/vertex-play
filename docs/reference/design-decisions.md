# Design Decisions

Architectural choices that affect pipeline cost, complexity, and feasibility on the lab. Each must be resolved explicitly — not deferred or assumed.

## 1. Where the Android toolchain runs

| Option | Description | Trade-off |
|--------|-------------|-----------|
| **A. Dockerized build** | Builder image contains JDK + SDK. Jenkins runs builds inside the container via Docker socket. Image stored at `shadowlands:5000`. | Reproducible, versioned, aligns with existing Jenkins pattern. Larger image (~2-3 GB). |
| **B. SDK on lab host** | Android SDK installed directly on shadowlands. Jenkins builds run on the host. | Simpler for one-off experiments. Worse for reproducibility, harder to upgrade, pollutes the host. |

**Recommendation:** A. Matches how vertex-block and vertex-studio already build.

## 2. Instrumented / UI tests

| Option | Description | Trade-off |
|--------|-------------|-----------|
| **None (v1)** | JVM unit tests + lint + assemble only. | Lowest friction. No emulator complexity. |
| **Emulator on lab** | Headless emulator in Docker or on the host. | Needs KVM/CPU features, RAM, stable images. Often painful on non-standard hardware. |
| **External farm** | Firebase Test Lab or similar. | Extra accounts, cost, network egress from lab to Google APIs. |

**Recommendation:** None for v1. Revisit after core pipeline is proven (POC-D is optional for this reason).

## 3. Upload tool

| Option | Description | Trade-off |
|--------|-------------|-----------|
| **Gradle Play Publisher (GPP)** | Gradle plugin, configured in `build.gradle.kts`. | Native to Gradle. Couples upload to the build system. Plugin version churn. |
| **fastlane supply** | Ruby-based CLI tool. | Well-documented, widely used. Adds Ruby to the builder image. |
| **Raw Play Developer API** | Direct HTTP calls or a thin script. | No extra dependencies. More code to write and maintain. |

**Recommendation:** Defer until POC-C. Try GPP first (least friction if the app already uses Gradle).

## 4. Branch / promotion model

| Trigger | Pipeline behaviour |
|---------|--------------------|
| Pull request | Build + test only |
| Merge to main | Build + test + sign (artifact stored, not uploaded) |
| Git tag | Build + test + sign + upload to internal track |

Production track promotion (internal → closed testing → production) is a manual Play Console action until trust in the pipeline is established.
