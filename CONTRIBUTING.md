# Contributing to gorm-cubrid

For issues, use clear type-only titles (`feat:`, `bug:`, etc.) without module scope and state observable acceptance criteria. PRs and commits use the repository's Conventional Commits/CI rules. Read the [shared issue, triage, PR and commit standard](https://github.com/cubrid-lab/.github/blob/main/docs/ISSUE_PR_COMMIT_STANDARD.md).

Thank you for your interest in contributing!

## Requirements

| Tool | Version | Purpose |
|---|---|---|
| Go | 1.21+ | Build and test |
| Git | any | Version control |
| CUBRID | 11.2+ | Integration tests only |
| Docker | any | Integration tests via `docker-compose` |

## Getting Started

```bash
git clone https://github.com/cubrid-lab/gorm-cubrid.git
cd gorm-cubrid
go mod download
```



## Test-driven implementation

For bug fixes or behavioral features, start from a reviewed interface/SQL/transaction design. Use the [TDD decision](docs/adr/0004-test-first-development.md): **Red** (run a new focused test and capture the genuine target failure), **Green** (smallest production change passing that test), **Refactor** (rerun related tests and final PR CI). In the PR description record the failing assertion/error **actually observed**, the passing assertion, which regressions ran, and whether real CUBRID validation ran or remains unverified. A mock-only pass cannot establish server compatibility; the exact release candidate still requires full live DB validation. No deliberate failing tests or skipped release checks may be left to satisfy the TDD process. For documentation and upstream feasibility work, describe the design/probe evidence rather than forcing an irrelevant Red test. The ADR and design contracts are proposals until reviewed and accepted.

## CUBRID correctness and regression evidence

For changes to CUBRID protocol, connection lifecycle, error handling, results, native SQL, ORM migrations, supported versions or releases, read [this repository's engineering invariants](docs/ENGINEERING_INVARIANTS.md) **before** implementation and review. The checks are adapted from the real bugs and tests of `pycubrid` and `sqlalchemy-cubrid`, not a Python API port. In the pull request, describe the behavioral invariant affected, the normal and failure cases tested, and which checks were offline, real CUBRID or independently compared with JDBC. State the exact CUBRID/runtime/driver versions and test outcomes when claiming database compatibility. **Do not mark tests as passed when they were skipped, missing or never executed.**

For documentation-only or nonbehavioral changes, explain why a database run is unnecessary rather than manufacturing an integration result. The final **release candidate** still follows the repository's full live-CUBRID release gate, separate from ordinary PR path optimization. These expectations supplement, and do not weaken, the established review/CI rules.

## Running Tests

### Unit tests (no CUBRID server needed)

```bash
go test ./...
# or, with verbose output:
go test -v -race ./...
```

All offline tests must pass before submitting a pull request.
Target coverage: **90%+** for non-integration code.

### Integration tests (CUBRID server required)

The repository currently has **no test files tagged `//go:build integration`** and no checked-in Docker Compose file. The CI workflow defines a CUBRID 11.3 service on push and invokes `go test -tags integration`, but without tagged test files this is not evidence of real database behavior coverage. Do not claim `AutoMigrate`, CRUD, or schema introspection has been validated against CUBRID. Adding those scenarios requires a separate implementation and reviewed tests.

## Code Style

- Format: `gofmt` / `goimports` (run before every commit)
- Lint: `go vet ./...`
- No external linters are required, but the CI gate enforces `go vet` clean output

```bash
make lint   # runs go vet
make fmt    # runs gofmt -w
```

## Commit Messages

Follow the conventional commits style used in this repository:

```
<type>: <short summary>

[optional body]
```

Types: `feat`, `fix`, `test`, `docs`, `refactor`, `chore`

Examples:
```
feat: add support for CUBRID ENUM type
fix: correct MODIFY COLUMN syntax for nullable columns
test: add integration test for AutoMigrate with associations
docs: update DSN format in README
```

## Pull Request Review and Merge Policy

All contributors follow the same process regardless of whether they use
OpenCode, Codex, Claude Code, ChatGPT, Copilot or no AI tool. Keep a PR
focused, provide relevant tests and affected docs, and describe validations
not performed locally. **Required GitHub Actions CI** is the authoritative
test record for the final head; local runs are recommended but not an extra
merge gate. Oracle, Copilot and `agent-review` may help reviewers but their
results are advisory, not required tool-specific approvals. The responsible
maintainer checks correctness, tests, security, maintainability, CI and
outstanding review findings before merging. Changes affecting credentials,
workflow write permissions or release/publishing operations receive explicit
independent maintainer scrutiny. Existing branch protection and repository
test commands remain unchanged.

## Pull Request Checklist

Before opening a PR, please verify:

- [ ] `go build ./...` succeeds
- [ ] `go vet ./...` is clean
- [ ] `go test -race ./...` passes (all unit tests)
- [ ] New behaviour is covered by tests
- [ ] `CHANGELOG.md` is updated under `## [Unreleased]`
- [ ] If adding a CUBRID-specific DDL behaviour, the reason is documented
      in a code comment (CUBRID does not always match MySQL/PostgreSQL syntax)

## Reporting Issues

When reporting a bug, please include:

- Go version (`go version`)
- CUBRID server version
- GORM version (`go list -m gorm.io/gorm`)
- Minimal reproduction snippet
- Full error output or stack trace

## Architecture Notes

See [CLAUDE.md](CLAUDE.md) for a detailed breakdown of the package architecture,
CUBRID-specific gotchas, and overridden Migrator methods.
