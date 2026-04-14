# Planning status

Last updated: 2026-04-14

## What's done

- All 8 epics outlined and ordered in `docs/epics.md`
- E1 (Lab integration) tickets written: 001–004 in `docs/epics/01-lab-integration/tickets/`
- E2 (Builder image) epic file and tickets written: 005–010 in `docs/epics/02-builder-image/tickets/`
- E2 tickets reference `docs/implementation/builder-image.md` (T/G/P numbers) per ADR-004 rather than duplicating content
- ADR-001's validation target change (architecture-samples → test-app) accounted for in E2 tickets: version pins (006) drive the choices, E3 writes the test app to match, E2 validation (009) depends on E3
- E3 (Test app fixture) epic file and tickets written: 011–012 in `docs/epics/03-test-app-fixture/tickets/`
- E4 (Play enrollment) epic file and tickets written: 013–016 in `docs/epics/04-play-enrollment/tickets/`

## What's next

- E5 (Signing) ticket breakdown — POC-B territory; upload keystore, Gradle signing config, Play App Signing enrollment
- Then E6 (Play upload), E7 (Reference app pipeline), E8 (Handoff)

## Key decisions

- E1 has 4 tickets (001–004), including a contingent insecure-registries ticket (004)
- E2 Dockerfile and Jenkinsfile combined into one ticket (007) since they land on the same branch
- E2 version pins are chosen from current stable releases rather than derived from an existing project, since the test app (E3) doesn't exist yet — E3 writes to match E2's choices
- E3 is two tickets: scaffold (011) then verify locally (012) — mirrors E2's "write then prove" pattern (007/008). Small epic because the app is intentionally minimal
- E4 includes creating the Play Console app listing (015), which supersedes spec.md section 3c's "throwaway app" in line with ADR-001 — uses E3's `applicationId`, not a temporary one
