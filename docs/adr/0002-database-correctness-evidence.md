# ADR 0002: CUBRID correctness and failure-evidence boundaries

- **Status:** Proposed
- **Date:** 2026-11-07
- **Scope:** gorm-cubrid correctness obligations and review evidence

## Context

The CUBRID Python driver and its SQLAlchemy dialect revealed that apparently successful queries can hide transaction-scope mistakes, accidental retries after uncertain execution, stale broker handles, precision loss and unverified ORM-generated SQL. The purpose of learning from them is to **reuse verified invariants and test design**, never Python API signatures or implementation code. The current repository has its own runtime, public API and released compatibility obligations.

## Decision

Own GORM Dialector, clause builders, schema catalog reflection and migrator logic, not CAS transport or connection pooling. Treat actual CUBRID SQL, DDL and transaction behavior as the authority. Unsupported ORM clauses cannot silently disappear. Verify real rollback of CUBRID DDL under the tested transaction/autocommit state, precision, affected rows and generated keys through the Go driver. A server container with no executed GORM test cases cannot validate AutoMigrate.

Use source-linked test cases as engineering references, then implement targeted **native-language** regression tests. Identify whether a test actually talked to CUBRID, which server/broker and client/ORM versions were used, what SQL/outcome was asserted, and whether any required case was skipped. The fact that a Python project passes its tests does **not** certify this package.

## Alternatives considered

- **Rejected:** Copying MySQL generated schema syntax; swallowing unsupported ON CONFLICT intent; trusting a green job with no integration-tagged test files; duplicating CAS/client logic inside GORM.
- **Rejected:** A generic cross-language implementation copied verbatim from Python.
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
