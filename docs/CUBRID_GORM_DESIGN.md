# Standalone CUBRID GORM dialect — proposed architecture

**Status:** Design proposal (2026-11-07), not an implemented compatibility guarantee. Parent issue: [#44](https://github.com/cubrid-lab/gorm-cubrid/issues/44).

## Product boundary

`gorm-cubrid` is a **Go GORM dialect**, not a database protocol driver. It consumes `cubrid-go` through the standard `database/sql` interface. Its SQL generation, model mapping and migrations are governed by the public GORM APIs and verified CUBRID behavior, not another ORM's architecture.

```text
Go application + GORM models
          |
GORM clause/callback/migration interfaces
          |
gorm-cubrid: CUBRID SQL, schema/type mapper, migration plan
          |
database/sql (Go pool + transaction facade)
          |
cubrid-go (physical CAS connections and execution)
          |
CUBRID
```

**No MySQL compatibility mode.** A generic GORM interface or builder may be reused when it is database-neutral, but any CUBRID-specific generated SQL must be justified by the CUBRID manual and verified on real CUBRID. Studying GORM's MySQL dialect as an example is permitted; silently forwarding SQL generation to it is not.

## Go/GORM extension decisions

1. **Dialect and clauses:** use GORM's public `gorm.Dialector`, `clause.ClauseBuilder`, callbacks and schema mapping. Review INSERT/defaults/generated IDs, WHERE/JOIN, conflict/upsert semantics, quoting, identifier binding and unsupported SQL. Do not silently ignore an unsupported `ON CONFLICT` expression.
2. **Data types:** own CUBRID type rules, nullable values, numeric precision, time, collections, LOBs and serialization failures. The underlying driver owns wire decoding and connection errors.
3. **Transactions:** GORM calls through `database/sql`. No second transaction/session manager; ensure ORM operations on a `*sql.Tx` cannot escape to another connection. Propagate underlying errors and ambiguous outcomes.
4. **Schema introspection:** inspect CUBRID `db_class`, `db_attribute`, `db_index` and version-dependent catalog views; verify schema owner/name and permissions, not merely return false on query failure.
5. **Migrator:** use CUBRID DDL, review auto-commit and data-loss risk. Separate safe additive migration from destructive changes, report unsupported operations explicitly and include reproducible migration tests.
6. **Duplicate implementations:** `cubrid-go/dialector` and this repository currently overlap. Adopt this repo as the proposed **future canonical GORM adapter**, with a compatibility inventory and staged user migration. Do not remove or deprecate the original until consumer impact is confirmed.

## Existing code, not proven support

`cubrid.go` already implements GORM Dialector methods. `migrator.go` embeds GORM's common migrator and queries CUBRID metadata. These establish a starting implementation, but not a blanket claim of safe AutoMigrate across CUBRID versions.

Existing [real-CUBRID test issue #43](https://github.com/cubrid-lab/gorm-cubrid/issues/43) is part of the release gate. No correctness claim should rest solely on mock SQL strings.

## Compatibility and versioning

`gorm-cubrid` and `cubrid-go` should target a **tested v1 compatibility generation**, not matching v1 minor/patch digits and not a synchronized release tag. The adapter must declare tested minimum Go/GORM versions and a driver contract when imported directly. Go's semantic import versioning means a future v2 of either module needs a suitable `/v2` path.

Maintain separate 0.x compatibility obligations and migration notes. Review current module path inconsistencies before changing them; see [policy issue #40](https://github.com/cubrid-lab/gorm-cubrid/issues/40).

## Release evidence checklist

- [ ] Dialect-generated SQL executed against selected real CUBRID versions, with expected rows, values and errors.
- [ ] GORM CRUD, associations, soft delete, default values, generated keys, transactions and rollback tested.
- [ ] CUBRID schema catalog, indices, keys and migrations verified with version/permission cases.
- [ ] Driver+GORM version matrix, no silent unsupported syntax or data loss, documentation of known limitations.
- [ ] Contribution examples follow idiomatic Go, and migration from the in-driver Dialector is documented.
- [ ] Compatibility changes have appropriate Go module versioning and non-breaking transition.

This proposal changes **no runtime behavior or released API**. Implement after a reviewed feasibility/behavior matrix with smaller independent issues.
