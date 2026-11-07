# ADR 0002: CUBRID correctness and failure-evidence boundaries

- **Status:** Proposed
- **Date:** 2026-11-07
- **Scope:** gorm-cubrid correctness obligations and review evidence

## Context

gorm-cubrid owns the Go GORM CUBRID dialector and migrator. This project's correctness decisions must cover GORM public extension APIs, Go database/sql driver integration, CUBRID SQL/DDL, module compatibility, and real ORM migrations, including negative and failure-path behavior. Passing offline tests alone is not proof of database compatibility. The accepted public API, local implementation, and actual CUBRID server behavior are the basis for requirements and test evidence.

## Decision

Own GORM Dialector, clause builders, schema catalog reflection and migrator logic, not CAS transport or connection pooling. Treat actual CUBRID SQL, DDL and transaction behavior as the authority. Unsupported ORM clauses cannot silently disappear. Verify real rollback of CUBRID DDL under the tested transaction/autocommit state, precision, affected rows and generated keys through the Go driver. A server container with no executed GORM test cases cannot validate AutoMigrate.

Create targeted tests for this project's observable contracts. Record whether tests actually communicated with CUBRID, the server/broker and dependency versions, asserted SQL outcomes, and skipped cases. External test results never certify this package.

## Alternatives considered

- **Rejected:** Copying MySQL generated schema syntax; swallowing unsupported ON CONFLICT intent; trusting a green job with no integration-tagged test files; duplicating CAS/client logic inside GORM.
- **Rejected:** Treating another package's API, implementation, or test results as this project's contract.
- **Rejected:** Marking an unsupported/unverified feature as absent simply because one server or connector failed in a particular test.

## Consequences and trade-offs

The adapter needs independent live migration and SQL tests and a reviewed transition for users of cubrid-go/dialector; fixes avoid persistent schema damage or misleading result metadata. Ordinary documentation-only PRs need not run an expensive database matrix; **the final release candidate still must run all required real-CUBRID checks** for its promised compatibility.

## Verification / acceptance criteria

- [ ] Normal and failure-path tests cover the actual decision's affected behavior.
- [ ] No silent statement replay, fabricated database values, unknown-success transaction or stale-handle reuse for the supported public API.
- [ ] Real DB tests use an isolated ready-checked CUBRID instance, assert actual stored/returned values and report nonzero executed-case counts; skip/missing is not success.
- [ ] Independent JDBC (or verified official driver) comparisons evaluate meaning and type, not matching source implementation.
- [ ] Public docs, type declarations and compatibility claims are reconciled with observed behavior; unsupported operations fail clearly.
- [ ] Maintainer accepts this ADR and separately reviews release/code changes; neither this record nor a checklist fixes existing defects.

## References

[engineering checks](../ENGINEERING_INVARIANTS.md), [GORM integration #43](https://github.com/cubrid-lab/gorm-cubrid/issues/43), [native design #44](https://github.com/cubrid-lab/gorm-cubrid/issues/44)

**No claim of implementation completion is made.**
