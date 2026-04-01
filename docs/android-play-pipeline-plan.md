# Android to Google Play pipeline plan

**Purpose:** Single link target for the full investigation and checklist: **vertex-play** (this repo) plus **vertex-studio** lab infra (Jenkins, Docker registry).

### Tracking this work

- **Checklist lives here.** The phases and IDs below (P, I, II, III, IV, V, A–D, F—about **74** rows in the granular index) are enough for day-to-day progress. Reference IDs in commits, PRs, or chat (e.g. “closes scope for **A-06**”).
- **Optional board (light mirror only).** If you want issues/epics in **Taiga**, GitHub, Jira, or similar: create **a handful of epics**, not one ticket per row. Practical splits: **one epic per phase** (e.g. “Phase II — Play API”) and/or **one per POC** (A–D). Each epic description should **link to this file** (`docs/android-play-pipeline-plan.md` on the default branch). Put detail in the doc; use the tracker for **status and blocking**, not a full duplicate list.
- **Taiga on the lab.** Vertex-studio deploys [Taiga](../vertex-studio/docs/playbooks/taiga.md)—a natural place for those epics. Add stories manually, or use **Taiga’s REST API** if you automate imports later. **Do not** bulk-write Taiga’s **database** directly (upgrades and schema changes break ad-hoc SQL; notifications and permissions won’t match).
- **What not to do:** Mass-create ~74 issues; raw DB dumps into any tracker. The doc **will** change—keep the file and any tracker epics in sync when you revise scope (see **Living document** below).

**Editor note:** A copy may also exist under `.cursor/plans/` locally; use **`docs/android-play-pipeline-plan.md`** on the default branch when sharing URLs.

**Living document:** Early-stage draft. The checklist and design will shift as you run POCs, enroll in Play, and add a real Gradle project. Treat Play policies, signing, and tax/regulatory steps as **authoritative from Google / your advisers**, not from this file. Anything produced in pairing (including by an assistant) can be wrong or outdated—**verify** before you rely on it.

---

## Current state (verified in repo)

- **[vertex-studio](../vertex-studio)** is Ansible-deployed services on a lab host; **Jenkins** runs in Docker with the **host Docker socket** mounted (`[roles/jenkins/templates/docker-compose.yaml.j2](../vertex-studio/roles/jenkins/templates/docker-compose.yaml.j2)`), and the root `[Jenkinsfile](../vertex-studio/Jenkinsfile)` shows tag-triggered `docker build` / `docker push` to `shadowlands:5000`.
- **Private registry** is optional (`make registry`); [docker-registry-investigation.md](../vertex-studio/docs/design/docker-registry-investigation.md) documents HTTP registry, `insecure-registries`, and Docker 29 / containerd snapshotter caveats—relevant if you **cache a custom Android build image** in that registry.
- **No Android, Gradle, or Play-related** references exist under `vertex-studio` today; the design is greenfield relative to that repo.
- **This repo** (`vertex-play`) is the **Android / Play pipeline** home (scaffold `README.md` + `LICENSE` as of plan update); **no Gradle project yet**—Phase I / POC-A still need an app module or checked-in sample.

This plan treats “full blown pipeline **up to** Play submission” as: **reproducible CI that produces a policy-valid release bundle and can deliver it to a Play track**, plus an explicit **human/console checklist** for verification and listing (policies, assets, questionnaires)—because Google does not fully automate “placement on the store” without console steps and review.

### Ownership: git and platform operations (you, not the plan executor)

The checklist below is still **your** work to track. In normal pairing, **no git operations** (new repos, branches, remotes, `jenkins_repos` edits, commits), **no GitHub webhook or PAT changes**, and **no Ansible vault edits** are implied unless you explicitly ask for drafted files or commands and run them yourself.

**Operator-owned (typical):**

- GitHub: repo creation, permissions, webhooks, PAT scopes, adding the app repo to `jenkins_repos` in [vertex-studio inventory](../vertex-studio/inventory/group_vars/all/vars.yaml), redeploy Jenkins (`make jenkins`), multibranch job visibility in Jenkins UI.
- Secrets: creating Jenkins credentials (keystore, Play service account JSON), vault password handling, keystore backup storage.
- Google: Play Console app creation, GCP linking, service account keys, IAM in Console, store listing and policy forms.

**Pairing / implementation can still help with:** Dockerfile and `Jenkinsfile` content, Gradle signing patterns (without secrets), lab notes under `docs/design/`, and concrete file diffs you choose to apply and commit.

Phase **P** todos remain **naming and documentation decisions** you make when you touch git or the vertex-studio README; they are not a request for someone else to create repos for you.

### Recorded context (clarified with B)

- **App repo:** **this repository** — scaffold; no Gradle tree yet. Add a minimal Android project in-repo or document an external sample path; refresh **I-02–I-09** once `app` + `build.gradle.kts` (or Groovy) land.
- **Play Console:** Not enrolled yet — **Phase II** starts with **developer program signup** (account requirements, one-time registration fee, acceptance into Play Console) before **GCP linking** and the Developer API. Add **II-00** (enrollment complete, Console reachable) as a gate before **II-03**.
- **P-01:** Done — scope recorded in [scope.md](scope.md) (single repo for app + CI + builder image + docs; `vertex-play-ci` deferred).
- **P-02:** Done — Git/lab boundaries in [repo-boundaries.md](repo-boundaries.md).
- **P-03:** Done — listed under Vertex apps in [vertex-studio README](../vertex-studio/README.md#vertex-apps-roadmap).
- **I-01:** Done — baseline in [i-01-baseline.md](i-01-baseline.md) (remote, checkout branch; `applicationId`/namespace TBD until Gradle exists).
- **Repo working name:** **vertex-play** — Android app + Google Play pipeline.
- **Australia:** Enrollment and **identity verification** depend on **Personal vs Organization** in Play Console and on Google’s **country-specific document list**—not a single “ABN required” rule for everyone. **Organization** accounts typically need **business registration** evidence (and Google often asks for a **D-U-N-S** number for orgs); Australian companies usually have **ASIC / ABN-related paperwork** in that picture, but the authoritative list is Google’s page for Australia, not this plan. **Personal** developer accounts commonly use **personal ID** (and possibly proof of address) without trading as a registered business. **ABN / GST** for **tax or merchant-of-record** reasons when selling paid products is separate from “can I open a dev account”; confirm with a **qualified tax adviser** if you are unsure. Official starting point: [Google Play Developer Verification — documents by country/region](https://support.google.com/googleplay/android-developer/answer/15633622) (select **Australia**).

### Granular task index (todo IDs)

The plan todo list has **74** checkable items; prefix encodes phase:

- **P-01 … P-03** — Repo / product boundaries (`vertex-play` naming) (3)
- **I-01 … I-09** — App baseline / build contract (9)
- **II-00, II-00a, II-01 … II-09** — Play enrollment (incl. Australia verification checklist) + GCP + service account (11)
- **III-01 … III-07** — Signing model + Jenkins wiring (7)
- **IV-01 … IV-08** — Policy, listing, submission readiness (8)
- **V-01 … V-06** — Lab capacity + Docker/registry + emulator decision (6)
- **A-01 … A-09** — POC-A Docker toolchain + `bundleRelease` (9)
- **B-01 … B-06** — POC-B CI signing + verify AAB (6)
- **C-01 … C-06** — POC-C upload tool + internal track (6)
- **D-01 … D-04** — POC-D instrumented tests, optional (4)
- **F-01 … F-05** — Final architecture + spec + risks + doc index (5)

Suggested order: **V** and **POC-A** can start once **vertex-play** has (or references) a buildable Android tree; **II-00** (Play enrollment) in parallel. **I** tracks **vertex-play** as **I-01** path is known; rerun **I** if project layout changes. **II-01+** after **II-00**, **III**, **IV** in parallel where possible, **A → B → C**, **D** if needed, **F** last. **P-02–P-03** when you document boundaries and update [vertex-studio README](../vertex-studio/README.md) Vertex apps table / link.

### Related docs (split out; no duplicate checklist here)

| Doc | Contents |
| --- | --- |
| [android-play-pipeline-submission.md](android-play-pipeline-submission.md) | CI vs Play Console “two halves”, mermaid diagram |
| [android-play-pipeline-blocks.md](android-play-pipeline-blocks.md) | JDK/SDK/Gradle/signing/API table |
| [android-play-pipeline-forks.md](android-play-pipeline-forks.md) | Docker vs host SDK, tests, upload tool, branch model |
| [android-play-pipeline-vertex-studio.md](android-play-pipeline-vertex-studio.md) | Jenkins/registry/secrets/network; repo shape vs **vertex-play** |
| [android-play-pipeline-phases.md](android-play-pipeline-phases.md) | Investigation phases I–V |
| [android-play-pipeline-pocs.md](android-play-pipeline-pocs.md) | POC A–D, deliverables, open decisions |
