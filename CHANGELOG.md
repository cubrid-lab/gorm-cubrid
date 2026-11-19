# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Upgrade notes

- **Module path moved to `github.com/cubrid-lab/gorm-cubrid`.** The repository
  lives under the `cubrid-lab` organization, and starting with the first
  release after v0.1.0 the Go module path is `github.com/cubrid-lab/gorm-cubrid`.
  v0.1.0 keeps its published path, `github.com/cubrid-labs/gorm-cubrid`, and
  stays fetchable under it through the module proxy and the GitHub redirect.
  Newer releases exist only under the new path: requesting one through the old
  path fails with a "module declares its path as
  github.com/cubrid-lab/gorm-cubrid" error. To migrate:

  ```sh
  go get github.com/cubrid-lab/gorm-cubrid@latest
  grep -rl 'github.com/cubrid-labs/gorm-cubrid' --include='*.go' . \
    | xargs sed -i 's#github.com/cubrid-labs/gorm-cubrid#github.com/cubrid-lab/gorm-cubrid#g'
  go mod tidy
  ```

  On macOS use `sed -i ''`. The driver import does not change yet: keep
  `import _ "github.com/cubrid-labs/cubrid-go"` until cubrid-go publishes a
  release under `github.com/cubrid-lab/cubrid-go`; the documentation here
  switches to the new driver path at that point.

### Documentation
- Add the CUBRID Lab brand icon to the README.

## [0.1.0] - 2026-03-12

### Added

- `Open(dsn string)` constructor — creates a Dialector from a CUBRID DSN string
- `New(config Config)` constructor — creates a Dialector from a `Config` struct,
  supporting custom driver name, existing `*sql.DB` connection pool, and
  default string size override
- `gorm.Dialector` interface implementation for CUBRID 11.2+:
  - `DataTypeOf` — maps GORM schema types to CUBRID SQL types
    (`TINYINT`, `SMALLINT`, `INT`, `BIGINT`, `FLOAT`, `DOUBLE`, `NUMERIC`,
    `VARCHAR`, `CLOB`, `DATETIME`, `BLOB`)
  - `BindVarTo` — MySQL-compatible `?` positional placeholders
  - `QuoteTo` — backtick identifier quoting with dot-notation and escape support
  - `Explain` — variable substitution for SQL logging
  - `DefaultValueOf` — `DEFAULT` keyword expression
- `Migrator` — CUBRID-specific schema management:
  - `CurrentDatabase` — queries current database name via `SELECT database()`
  - `GetTables` / `HasTable` — `INFORMATION_SCHEMA.TABLES`-based introspection
  - `HasColumn` — `INFORMATION_SCHEMA.COLUMNS`-based introspection
  - `HasIndex` — `db_index` system catalog-based introspection
  - `ColumnTypes` — full column metadata from `INFORMATION_SCHEMA.COLUMNS`
  - `RenameTable` — CUBRID-native `RENAME TABLE old TO new`
  - `DropIndex` — CUBRID-required `DROP INDEX idx ON table` syntax
  - `AlterColumn` — CUBRID-native `ALTER TABLE t MODIFY COLUMN col type`
- Connection validation via `Ping()` on `Initialize`
- Unit test suite (38 tests, no CUBRID server required)

[Unreleased]: https://github.com/cubrid-lab/gorm-cubrid/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/cubrid-lab/gorm-cubrid/releases/tag/v0.1.0
