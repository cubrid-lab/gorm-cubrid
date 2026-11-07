# Engineering invariants for gorm-cubrid

> **Status:** Proposed repository-specific engineering checklist, not evidence that existing tests pass.

gorm-cubrid owns the Go GORM CUBRID dialector and migrator. Correctness requirements follow GORM public extension APIs, Go database/sql driver integration, CUBRID SQL/DDL, module compatibility, and real ORM migrations. Use this repository's implementation, public API, relevant live-CUBRID evidence and official CUBRID/JDBC results as appropriate; never assume that another language's driver or ORM test result establishes behavior here.

## Evidence and adoption method

The Python projects' reusable contribution is **how correctness was established**, not a shared API: model physical CAS lifecycle, test uncertain SQL execution without replay, invalidate stale handles, verify transaction rollback, preserve type fidelity, compare semantic outcomes with an independent official driver/JDBC, refuse vacuous live-test passes, and publish only after final candidate checks.

Sources: [CAS reconnect/session tests](https://github.com/cubrid-lab/pycubrid/blob/main/tests/test_cas_session_persistence.py), [cursor handle ownership](https://github.com/cubrid-lab/pycubrid/blob/main/tests/test_autocommit_handle_ownership.py), [live state machine](https://github.com/cubrid-lab/pycubrid/blob/main/tests/test_connection_state_machine.py), [official driver differential](https://github.com/cubrid-lab/pycubrid/blob/main/tests/test_official_differential.py), [SQLAlchemy CUBRID architectural boundaries](https://github.com/cubrid-lab/sqlalchemy-cubrid/blob/main/docs/ARCHITECTURE.md), [DDL rollback tests](https://github.com/cubrid-lab/sqlalchemy-cubrid/blob/main/test/test_transactional_ddl.py), [integration gate tests](https://github.com/cubrid-lab/sqlalchemy-cubrid/blob/main/test/test_integration_gate.py), [release note style](https://github.com/cubrid-lab/sqlalchemy-cubrid/blob/main/docs/RELEASE_NOTES_STYLE.md).

Do **not** infer CUBRID support from syntactic similarity with MySQL. In particular, CUBRID DDL rollback is exercised by real CUBRID behavior verified for the claimed database version; do not state that CUBRID always auto-commits DDL without version-and-transaction-specific real evidence.

## GORM-specific invariants

1. **Clear ownership:** `gorm-cubrid` is the GORM dialect and migrator, while `cubrid-go` provides the underlying `database/sql` connection. It must not duplicate the CAS protocol or create a second pool/transaction controller. Migrate consumers of `cubrid-go/dialector` deliberately, not by silently breaking imports.
2. **No silent SQL feature loss:** current `cubrid-go/dialector` ignores `ON CONFLICT`; a GORM adapter must never discard unsupported SQL intent and report success. Verify behavior on *this* repo, not by assuming its separate implementation has the same bug. Supported conflict/update/default clauses must compile to actual CUBRID SQL.
3. **DDL is data correctness:** check catalog views and real CUBRID transaction behavior; test schema creation, rollback, change, index, constraints and destructive migration safeguards. Do not inherit MySQL assumptions about DDL auto-commit. Preserve GORM model and schema compatibility for existing users.
4. **Driver results:** `RowsAffected`, generated keys, NULL, 64-bit integer, precise NUMERIC, Unicode, dates and collections must reflect actual driver/CUBRID values. Never fabricate insert IDs from a generic count or convert an unsupported type to a misleading default.
5. **Transactions:** execute all GORM statements on the correct `sql.Tx` and physical CAS connection. Propagate begin/commit/rollback errors; test failed commit, rollback and concurrent users. No query should escape to autocommit.
6. **Truthful release claims:** a workflow that starts CUBRID but runs no integration assertions is not a database test. Require actual CRUD/relations/migration round trips, executed test counts and release candidate SHA.

## GORM PR checklist

- [ ] GORM clause/dialect code generation is matched against CUBRID manual and live DB behavior; errors rather than unsupported silent success.
- [ ] Model migration changes include pre/post schema SQL evidence, transactional rollback and safe failure behavior; separate destructive tests use disposable DBs.
- [ ] Unit/race tests and live Go+GORM integration tests assert rows, data types and transaction outcomes with the actual driver version.
- [ ] Confirm both old/new dialect import paths and public GORM behavior for compatibility changes.
- [ ] Record CUBRID version, actual SQL, test cases passed/failed/skipped, known limits and candidate SHA before publishing.
- [ ] Maintain release notes, accurate status/support labels and external contributor bylines from verified merged PR authors.

Tracked: [live DB #43](https://github.com/cubrid-lab/gorm-cubrid/issues/43), [compatibility #40](https://github.com/cubrid-lab/gorm-cubrid/issues/40), [architecture #44](https://github.com/cubrid-lab/gorm-cubrid/issues/44). Follow the repository [release policy](RELEASE_POLICY.md); the separate [GORM architecture PR #45](https://github.com/cubrid-lab/gorm-cubrid/pull/45) remains a design proposal.
