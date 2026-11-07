# Development setup — Go GORM CUBRID dialect

> **Status:** Matches current `go.mod`, `Makefile`, CI and contributor guidance; **not a claim that the commands or DB tests have run**. [ADR 0005](adr/0005-development-setup.md) is Proposed.

## Toolchain and clean checkout

Use Go **1.21 or later** (CI: 1.21/1.22/1.23). This repository defines its own `gorm.io/gorm` dependency; the DB driver and GORM dialect have distinct ownership. Current `go.mod` module name is `github.com/cubrid-labs/gorm-cubrid` although the GitHub repository owner is `cubrid-lab`; do **not** silently change the import path during setup.

```sh
git clone https://github.com/cubrid-lab/gorm-cubrid.git
cd gorm-cubrid
go version
go mod download
go build ./...
go vet ./...
go test -race -count=1 ./...
```

Do not use `make check-all` as a nonmutating validation: it invokes `gofmt -w .`. Existing `make doctor` shows optional tools but isn't a substitute for CI. If `go mod download` fails because of module identity or network resolution, record the result and resolve the dependency contract instead of hiding the error with an unreviewed `replace`.

## Real CUBRID integration is not ready

There is **no checked-in Docker Compose** and **no Go tests marked `//go:build integration`** on the inspected main branch. The CI contains a *push-only* CUBRID 11.3 service and runs `go test -tags integration`, but a command that executes only existing unit tests does not prove GORM SQL, schema or migration integration.

The current Makefile includes `make test-integration` with a default `CUBRID_DSN=cubrid://dba:@localhost:33000/demodb`. That command is merely an entry point for future tagged tests. Do not report it as a live validation success until [#43](https://github.com/cubrid-lab/gorm-cubrid/issues/43) adds real, nonvacuous CRUD, transaction, generated-key and schema/migration cases. Use disposable isolated CUBRID tables/databases, never production data.

## ORM setup and compatibility boundaries

- The Go driver owns `database/sql` transport; GORM's `Dialector`, clause generation and `Migrator` live here.
- The `cubrid-go/dialector` overlap requires a staged user compatibility plan, not an immediate removal.
- Test a fresh external consumer with the **published** or actually importable module paths and an explicitly selected driver version before declaring 1.0.

## Setup exit criteria

- [ ] Go 1.21 minimum builds, tests, vets and race-checks on a clean checkout.
- [ ] No accidental changes to `go.mod` or `go.sum` during setup.
- [ ] Module import/driver dependencies are proven in a fresh consumer.
- [ ] CUBRID integration harness and tests exist and run with nonzero actual SQL assertions.
- [ ] See [architecture PR #45](https://github.com/cubrid-lab/gorm-cubrid/pull/45), [release policy](RELEASE_POLICY.md), [design sign-off](https://github.com/cubrid-lab/gorm-cubrid/blob/docs/cubrid-native-gorm-design/docs/DESIGN_SIGNOFF.md).
