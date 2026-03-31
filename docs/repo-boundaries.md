# Repository boundaries (P-02)

What lives where so `vertex-play` and the lab stay unambiguous. See also [scope.md](scope.md).

## `vertex-play` (this repository)

- Android application source (Gradle `app` module when present).
- CI: root `Jenkinsfile`, optional shared pipeline snippets.
- Builder image: `Dockerfile` or `docker/` for the Android SDK image used on Jenkins.
- Documentation: `docs/` (plan, scope, this file, future `docs/design/` notes).
- Play delivery **configuration** (e.g. Gradle Play Publisher / fastlane) **without** secrets; signing keys and Play service account JSON stay in **Jenkins credentials** (or your chosen secret store), not in git.

## `vertex-play-ci` (optional, not used in v1)

- Reserved for a **future** split if the builder image should version and release on its own cadence.
- Until then, builder Dockerfile lives **in this repo** per [scope.md](scope.md).

## `vertex-studio` (sibling repo: `../vertex-studio`)

- **Infrastructure as code** and lab services: Ansible playbooks/roles, Jenkins **deployment**, Docker registry role, Taiga, MkDocs, etc.
- **Does not** hold the Android app or the `vertex-play` pipeline source.
- You add `vertex-play` to Jenkins multibranch via `jenkins_repos` and host webhooks as documented there—not in this file.

## Secrets and inventory

- **vertex-studio** may hold Ansible Vault for **lab** secrets (e.g. Jenkins admin, PAT for GitHub).
- **vertex-play** pipeline secrets (keystore, Play API JSON) are **operator-managed** in Jenkins; do not commit them here.

## Links

- [scope.md](scope.md) — product/repo scope (P-01)
- [android-play-pipeline-plan.md](android-play-pipeline-plan.md) — full checklist
- Lab: [vertex-studio](../vertex-studio)
