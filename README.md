# vertex-play

CI/CD pipeline kit for building, signing, and publishing Android applications to Google Play. Runs on the [vertex-studio](../vertex-studio) lab infrastructure (Jenkins, Docker registry).

This repo contains the pipeline tooling and documentation — not Android application source code. Individual app repos are inputs to the pipeline.

## Docs

- **[Pipeline specification](docs/spec.md)** — architecture, stages, implementation breakdown, POC sequence
- **[Scope](docs/scope.md)** — what lives in this repo
- **[Repo boundaries](docs/repo-boundaries.md)** — vertex-play vs vertex-studio separation
- **[Building blocks](docs/reference/building-blocks.md)** — JDK, SDK, Gradle, signing, Play API requirements
- **[Design decisions](docs/reference/design-decisions.md)** — toolchain, testing, upload tool, branch model
