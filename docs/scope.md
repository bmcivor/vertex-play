# vertex-play — repository scope (P-01)

**Status:** Recorded for v1; edit this file if strategy changes.

## Decision

**One repo** for the Android app and the automation that builds, signs, and (later) publishes to Google Play from the vertex-studio lab.

| Area | In this repo |
|------|----------------|
| Application | Gradle `app` module and sources (when added) |
| CI | Root `Jenkinsfile`, optional shared pipeline snippets |
| Builder image | `Dockerfile` or `docker/` for the Android SDK image used on Jenkins |
| Docs | `docs/` including [android-play-pipeline-plan.md](android-play-pipeline-plan.md) |
| Play tooling | Config in repo **without secrets**; keys in Jenkins only |

## Deferred

- **`vertex-play-ci`** separate repo — only if splitting the builder image is worth it.
- **vertex-studio** — infra only; no application source there.

## Links

- [android-play-pipeline-plan.md](android-play-pipeline-plan.md)
- Lab: sibling [vertex-studio](../vertex-studio)
