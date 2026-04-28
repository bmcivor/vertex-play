# Planning status

Last updated: 2026-04-28

## What's done

- All 8 epics outlined and ordered in `docs/epics.md`
- E1 (Lab integration) tickets written: 001–004 in `docs/epics/01-lab-integration/tickets/`
- E2 (Builder image) epic file and tickets written: 005–010 in `docs/epics/02-builder-image/tickets/`
- E2 tickets reference `docs/implementation/builder-image.md` (T/G/P numbers) per ADR-004 rather than duplicating content
- ADR-001's validation target change (architecture-samples → test-app) accounted for in E2 tickets: version pins (006) drive the choices, E3 writes the test app to match, E2 validation (009) depends on E3
- E3 (Test app fixture) epic file and tickets written: 011–012 in `docs/epics/03-test-app-fixture/tickets/`
- E4 (Play enrollment) epic file and tickets written: 013–016 in `docs/epics/04-play-enrollment/tickets/`
- E5 (Signing) epic file and tickets written: 017–021 in `docs/epics/05-signing/tickets/`
- E6 (Play upload) epic file and tickets written: 022–025 in `docs/epics/06-play-upload/tickets/`
- ADR-005 written: upload tool choice resolved as Gradle Play Publisher (GPP)
- E7 (Reference app pipeline) epic file and tickets written: 026–028 in `docs/epics/07-reference-pipeline/tickets/`
- ADR-006 written: consumption model resolved as copy-paste (@Library deferred)

## What's next

- E8 (Handoff) ticket breakdown — adoption doc, README/spec updates, lessons learned

## Key decisions

- E1 has 4 tickets (001–004), including a contingent insecure-registries ticket (004)
- E2 Dockerfile and Jenkinsfile combined into one ticket (007) since they land on the same branch
- E2 version pins are chosen from current stable releases rather than derived from an existing project, since the test app (E3) doesn't exist yet — E3 writes to match E2's choices
- E3 is two tickets: scaffold (011) then verify locally (012) — mirrors E2's "write then prove" pattern (007/008). Small epic because the app is intentionally minimal
- E4 includes creating the Play Console app listing (015), which supersedes spec.md section 3c's "throwaway app" in line with ADR-001 — uses E3's `applicationId`, not a temporary one
- E5 does not have a separate Play App Signing enrolment ticket — auto-enrolled at Play Console listing creation (015) since Google made it mandatory for new apps in Aug 2021
- E5 020 (signing validation) deliberately uses a throwaway Jenkins job, not the reference app-pipeline Jenkinsfile — that's E7's territory. Keeps POC-B scrappy and lessons feed into E7's design
- E6 upload tool is GPP (`com.github.triplet.play`) — see ADR-005. fastlane and raw API ruled out (Ruby runtime overhead and implementation cost respectively)
- E6 023/024 follow the same throwaway-job discipline as E5 020 — reference Jenkinsfile is E7's territory
- E7 consumption model is copy-paste (ADR-006) — @Library ruled out for lab scale, deferred as future migration path
- E7 versionCode uses Jenkins BUILD_NUMBER — monotonically increasing, no per-release discipline required
