# Parallel issue and PR execution review

> **Snapshot:** 2026-11-08. Current GitHub inventory: **6 open issues, 7 open PRs**. Recheck before acting; this document does not approve a design, code change or release.

## What can proceed concurrently

The real-CUBRID integration harness, Go module external consumer test, GORM clause and migrator design, CI change-impact routing, developer documentation and release-note proofreading are independent workstreams. **Only final implementation/merge** has technical dependencies on the versioned cubrid-go driver contract and tested GORM extension APIs. A policy ADR is a gate for final approval, **not** a global research or TDD RED-test blocker.

## Open issues

| Issue | Priority | Size | Workstream | Real coordination boundary |
| --- | --- | --- | --- | --- |
| [#44](https://github.com/cubrid-lab/gorm-cubrid/issues/44) feat: design a standalone CUBRID-native GORM dialector and migrator | high | M | Native GORM architecture | Investigate native SQL/DDL and dialector ownership now; final stable API needs documented consumer migration, not all tests before research. |
| [#43](https://github.com/cubrid-lab/gorm-cubrid/issues/43) test: implement real CUBRID integration cases before reporting database validation | high | M | Live CUBRID tests | Disposable CUBRID harness and tests start independently; final release must execute nonzero real SQL tests. |
| [#41](https://github.com/cubrid-lab/gorm-cubrid/issues/41) ci: limit Go test matrix to relevant changes and preserve main integration coverage | medium | M | CI routing | Docs-only CI routing is independent; main and release candidate integration must remain mandatory. |
| [#40](https://github.com/cubrid-lab/gorm-cubrid/issues/40) docs: define GORM adapter compatibility and Go module policy | high | small | Module/version contract | Verify Go module import identity and GORM/driver consumers in parallel with native dialect design. |
| [#28](https://github.com/cubrid-lab/gorm-cubrid/issues/28) chore: implement staged issue intake and label verification | medium | M | Issue intake | Standalone label/triage checks; reconcile shared issue templates on merge only. |
| [#24](https://github.com/cubrid-lab/gorm-cubrid/issues/24) ci: run the stale workflow under the cubrid-lab organization name | low | S | Maintenance | Low urgency stale workflow correction; not a dependency for ORM correctness. |

## Open PRs

| PR | Priority | Merge and risk notes |
| --- | --- | --- |
| [#47](https://github.com/cubrid-lab/gorm-cubrid/pull/47) docs: correct GORM release notes and retain historical support scope | medium | **Release-note documentation:** Independent historical v0.1.0 note validation. Coordinate only when editing same release documents; no tag or module change. |
| [#46](https://github.com/cubrid-lab/gorm-cubrid/pull/46) docs: establish GORM adapter release and compatibility rules | high | **Release policy:** Review policy/ADR on final head; passing unit CI with skipped integration is not live DB release evidence. |
| [#45](https://github.com/cubrid-lab/gorm-cubrid/pull/45) docs: define a standalone CUBRID-native GORM architecture (Draft) | high | **Native architecture Draft:** Research feasibility now; final approval follows release policy and CUBRID grammar verification. |
| [#42](https://github.com/cubrid-lab/gorm-cubrid/pull/42) ci: skip Go matrix for documentation-only pull requests | medium | **CI routing:** Separate from ORM SQL; only file-level workflow merge coordination. |
| [#29](https://github.com/cubrid-lab/gorm-cubrid/pull/29) chore(triage): mark and verify issue triage state | medium | **Triage workflow:** Can progress independently, retain reviewer-managed labels. |
| [#25](https://github.com/cubrid-lab/gorm-cubrid/pull/25) ci: run the stale workflow under the cubrid-lab organization name | low | **Stale workflow:** Low urgency; rerun failed security checks on final commit. |
| [#23](https://github.com/cubrid-lab/gorm-cubrid/pull/23) docs(brand): add role icon to README (Draft) | low | **Cosmetic docs Draft:** Low urgency, README may conflict with policy branch at merge. |

## Hard evidence and merge limits

The release/setup policy PR #46 currently has unit checks passing, but the live CUBRID integration job is **skipped**. Completing #43 with real SQL/transaction/DDL assertions is a release prerequisite but may start today, independently of architecture Draft #45. Do not remove a public module path or duplicate the cubrid-go in-driver GORM API before testing actual external consumers. The new release note PR #47 is independent of runtime features and should verify historical facts, not trigger a tag or package publish.
