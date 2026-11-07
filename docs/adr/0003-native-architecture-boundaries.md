# ADR 0003: Native CUBRID architecture boundaries

- **Status:** Proposed
- **Date:** 2026-11-07
- **Prerequisite:** Approval and merge of this repository's release policy ADR in its earlier release-policy PR
- **Decision scope:** CUBRID-specific GORM dialect, migrator and driver ownership

## Context

GORM needs an idiomatic Go Dialector/Migrator and CUBRID SQL semantics. cubrid-go provides the database/sql driver but also contains a dialector, creating an ownership overlap. The GORM project currently has a CUBRID integration job whose existence does not demonstrate tagged real DB test coverage.

This decision is grounded in gorm-cubrid's own public interfaces, repository code, and CUBRID correctness requirements. External implementations cannot substitute for local validation.

## Decision

Make gorm-cubrid the **proposed** canonical GORM integration: own GORM clauses/callbacks, CUBRID schema reflection and migration planning, while cubrid-go owns all CAS I/O and database/sql behavior. No SQL or migrations are deemed supported merely because MySQL emits similar syntax. Explicit errors are preferable to silently discarding unsupported conflict/upsert clauses. Preserve users of the older in-driver dialect until a reviewed migration strategy exists; support CUBRID transactional DDL with actual version-specific tests.

## Alternatives considered

Use a MySQL-backed GORM dialector as compatibility authority (rejected); implement another independent CAS client (rejected); treat the in-driver Dialector as already removed (rejected); trust a healthy CUBRID container without real test assertions (rejected).

## Consequences and trade-offs

A canonical ownership boundary helps avoid divergent SQL and migration bugs, but migration safety needs live data/schema tests and compatibility coordination. GORM can publish independently when its own release evidence is complete.

## Verification / acceptance criteria

Prove DML, JOIN, association, generated key/RowsAffected, transactions and AutoMigrate/catalog behavior on real CUBRID for advertised versions, including failed and transactional DDL cases. Verify consumer imports of the chosen GORM dialector with cubrid-go.

- [ ] Approve release/compatibility policy ADR 0001 **before** accepting this architecture ADR.
- [ ] The feasibility evidence and exact supported versions are recorded; unknown or unsupported behavior is not reported as implemented.
- [ ] Separate implementation issues, regression tests and release documentation agree with this decision.
- [ ] A maintainer reviews the decision and records acceptance explicitly; draft status alone never authorizes 1.0.

## References

[GORM native design](../CUBRID_GORM_DESIGN.md), [standalone design #44](https://github.com/cubrid-lab/gorm-cubrid/issues/44), [real CUBRID #43](https://github.com/cubrid-lab/gorm-cubrid/issues/43), [release policy PR #46](https://github.com/cubrid-lab/gorm-cubrid/pull/46)

**This ADR is an architecture proposal, not a runtime implementation, migration completion, CI pass or approved release.**
