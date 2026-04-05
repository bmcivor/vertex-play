# Scope

**Status:** Active.

## Decision

Single repo for the Android/Play pipeline tooling and documentation.

| Area | In this repo |
|------|--------------|
| Builder image | `docker/` — Dockerfile for the Android SDK build environment used by Jenkins |
| Pipeline | Root `Jenkinsfile` — reference pipeline definition for Android app builds |
| Upload tooling | Play upload config (GPP, fastlane, or scripts) without secrets |
| Docs | `docs/` — pipeline spec, reference material, POC lab notes |

## Not in this repo

| Area | Where it lives |
|------|----------------|
| Android application source | Separate app repos (e.g. a future `vertex-app-*`) |
| Lab infrastructure (Jenkins, registry, Ansible) | [vertex-studio](../../vertex-studio) |
| Secrets (keystores, Play API keys) | Jenkins credentials store |

## Deferred

- **`vertex-play-ci`** separate repo — only if the builder image needs its own release cadence independent of the pipeline definition.

## Links

- [spec.md](spec.md)
- [repo-boundaries.md](repo-boundaries.md)
