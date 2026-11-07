# Design sign-off and TDD acceptance plan

> **Status: Not signed off / design under review.** This plan captures *expected* test behavior, not tests already written or run. The repository release policy and ADR 0001/0002/0004 are proposed in the earlier policy PR. The native architecture ADR 0003 (for this repository, if applicable) is proposed in this design draft. No implementation, CI success, approved public API or live DB outcome is implied.

## Project setup prerequisite

**ADR 0005 and the development setup guide** are proposed in the earlier release-policy PR; check [the setup documentation](https://github.com/cubrid-lab/gorm-cubrid/blob/docs/release-policy-before-native-design/docs/DEVELOPMENT_SETUP.md) and the manifest/CI before checking these boxes.

- [ ] **Clean checkout verified:** language-native runtime/compiler and tooling are selected with a documented developer versus published consumer support distinction.
- [ ] **Dependency resolution verified:** exact lockfile/Go module/Cargo graph installs without undocumented replacement, incompatible version or incorrect import path.
- [ ] **Read-only setup checks executed:** record **actual** build/type/lint/unit results, not command examples; all known missing config/scripts have explicit blockers.
- [ ] **Real DB harness decided:** identify a disposable real CUBRID service and concrete test selection, or mark missing harness as blocked. A Docker healthcheck, fake broker or zero/fully skipped suite is not evidence.
- [ ] **External package consumer planned:** npm packed ESM/CJS/TypeScript, Go module imports or Cargo crate package resolution (as appropriate), with release preflight separate from initial setup.
- [ ] **Repository developer guidance consistent:** README, CONTRIBUTING, AGENTS, tooling and CI refer to commands that actually exist and do not silently raise the supported runtime floor.
- [ ] **Maintainer approval recorded** for ADR 0005 and any associated module-path, MSRV, dependency or CI changes.

These are **not completed** merely because the files are written. If setup is blocked, fix or explicitly scope it in a separate reviewed task before claiming the design is ready for implementation.

## Design-first gate

Before implementation changes are treated as approved, establish each of the following in reviewed project documentation and link the evidence. Check only when genuinely resolved, not merely because this template exists.

- [ ] **Boundaries and interfaces:** owned layer, public API, language-native patterns, cross-repository dependency, version and compatibility envelopes.
- [ ] **Protocol/SQL contract:** CUBRID-specific wire/query semantics, supported and unsupported behaviors, parameter/result mapping, resource lifetimes.
- [ ] **State and failure model:** connection/session/transaction/statement states, cancellation, retries, ambiguous outcomes and cleanup.
- [ ] **Data types and precision:** NULL, signed integers, exact numeric, text, date/time, collections, binary/LOB and errors.
- [ ] **Schema/migration contract (where applicable):** CUBRID DDL, introspection, reversible behavior, destructive change guardrails.
- [ ] **Security and operations:** credentials/log redaction, timeout limits, resource bounds, cancellation behavior and diagnostics.
- [ ] **Verification design:** red/green cases (below), real CUBRID versions, non-vacuous failure gate, independent JDBC semantic comparison when appropriate.
- [ ] **Release readiness:** published package/module/crate and supported consumer matrix, reviewed release notes, stable 1.0 exit conditions.
- [ ] **Upstream feasibility:** independently verify third-party ORM public extension APIs and unsupported methods before committing to architecture.
- [ ] **Review approval:** maintainers explicitly approve the language-native architecture ADR, test contract and open blockers; no speculative sign-off.

The PR/issue must distinguish **decided**, **proposed**, **blocked**, and **requires live verification**. If a significant feasibility constraint prevents the proposed direction, update the ADR and its alternatives before implementing a workaround.

## TDD acceptance cases

For each feature or bug: first run a targeted test that fails for the intended reason (**Red**), make the smallest implementation pass (**Green**), refactor under the same tests, and verify live database behavior before claiming compatibility. A red failure log may be summarized truthfully in the PR but need not be committed. The final PR must have green required checks.

| Test contract (proposed) | Expected observable outcome | First test layer | TDD starting point |
| --- | --- | --- | --- |
| GORM SQL clauses | Unsupported upsert/conflict syntax fails visibly rather than being dropped; supported SQL executes on CUBRID | GORM SQL builder unit + integration SQL round trip | red unsupported clause accepted silently |
| Results and associations | Generated IDs, RowsAffected, JOIN and nullable relation mapping match actual driver results | GORM model fixtures + live test | red wrong DML/association semantics |
| Transaction ownership | GORM transaction boundary keeps every statement on one sql.Tx; rollback discards data | fake sql driver and live two-connection test | red escaped-transaction write |
| Schema migration | CUBRID catalog views and CREATE/ALTER/index/constraint changes match actual schema; DDL rollback under known autocommit state | tagged integration tests with disposable DB | red AutoMigrate/cat mismatch, not a mock-only pass |
| Module and imports | Existing `cubrid-go/dialector` and `gorm-cubrid` imports have a safe staged compatibility plan | external Go consumer of both paths | document behavior before changing users |

## Project boundary

**Role:** Go GORM Dialector, CUBRID clauses and schema migrator.

GORM models/clauses/catalog belong here, CAS transport and physical transaction state belong to `cubrid-go`; duplicate existing in-driver Dialector must not be removed without consumer migration plan.

## Decisions still blocking full sign-off

#43 live GORM test cases missing, #40 policy/module ID mismatch, #44 design; real migration behavior and duplicate Dialector consumer inventory not verified.

These are outstanding verifications, not proof of failure. Existing code and currently declared support are not silently reclassified as unsupported merely because a test is absent.

## Release and implementation sequencing

1. Approve the repository release policy and [TDD ADR 0004](https://github.com/cubrid-lab/gorm-cubrid/blob/docs/release-policy-before-native-design/docs/adr/0004-test-first-development.md) in the release-policy PR.
2. Resolve the above design/feasibility blockers with evidence, review [GORM design proposal](CUBRID_GORM_DESIGN.md), and explicitly accept the architecture/contract ADR when applicable.
3. Implement each scoped issue with test-first evidence, one observable contract at a time. No implementation-wide PR before essential extension feasibility.
4. Run real CUBRID and downstream consumer tests on the final release candidate; document exact versions and test counts; do not publish based on mocks alone.

**Tracked design PR:** #45. This file is a **sign-off checklist**, not a signed-off design.
