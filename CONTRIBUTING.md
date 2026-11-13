# Contributing to gorm-cubrid

For issues, use clear type-only titles (`feat:`, `bug:`, etc.) without module scope and state observable acceptance criteria. PRs and commits use the repository's Conventional Commits/CI rules. Read [AGENTS.md](AGENTS.md) for this repository's review guidance.

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



## Reproducible development setup

Before changing code, follow [the repository-specific development setup](docs/DEVELOPMENT_SETUP.md) and [ADR 0005](docs/adr/0005-development-setup.md). Start from the committed language-native manifest and a **clean checkout**, run non-mutating local checks, and record the actual toolchain and results. Do not interpret a mock suite or missing/fully skipped integration tests as real CUBRID verification, and do not alter Go module imports, GORM API or driver dependency bounds or release tooling to work around an unverified local setup issue. The setup ADR is Proposed and awaits review.

## Test-driven implementation

For bug fixes or behavioral features, start from a reviewed interface/SQL/transaction design. Use the [TDD decision](docs/adr/0004-test-first-development.md): **Red** (run a new focused test and capture the genuine target failure), **Green** (smallest production change passing that test), **Refactor** (rerun related tests and final PR CI). In the PR description record the failing assertion/error **actually observed**, the passing assertion, which regressions ran, and whether real CUBRID validation ran or remains unverified. A mock-only pass cannot establish server compatibility; the exact release candidate still requires full live DB validation. No deliberate failing tests or skipped release checks may be left to satisfy the TDD process. For documentation and upstream feasibility work, describe the design/probe evidence rather than forcing an irrelevant Red test. The ADR and design contracts are proposals until reviewed and accepted.

## CUBRID correctness and regression evidence

For changes to GORM SQL/DDL, catalog and actual migration outcomes, read [this repository's engineering invariants](docs/ENGINEERING_INVARIANTS.md) **before** implementation and review. The requirements follow the GORM adapter and its own query/migration contracts, not another package's failure reports or test results. In the pull request, describe the behavioral invariant affected, the normal and failure cases tested, and which checks were offline, real CUBRID or independently compared with JDBC. State the exact CUBRID/runtime/driver versions and test outcomes when claiming database compatibility. **Do not mark tests as passed when they were skipped, missing or never executed.**

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


## Issue handoff and development progress

Use the issue and PR's actual state as evidence. Classification, authorization,
implementation, review and completion are **different decisions**:

1. **Intake / triage:** A newly filed issue starts with `status: needs triage`.
   Check evidence or reproduction, duplicate open/closed issues and overlapping
   PRs, repository scope, type, priority, size, dependencies, and verifiable
   acceptance criteria. A maintainer removes the intake label only after
   confirming the saved GitHub metadata. Do not infer priority, size, or triage
   completion automatically from the title or an AI suggestion.
2. **Ready:** Triage completion alone does not authorize development. Record
   the Ready decision in the issue only when the intended outcome and non-goals are bounded, blockers
   have been resolved, and any repository-specific product, architecture,
   safety or compatibility decisions have been accepted. Unresolved research
   remains investigation; blocked work records its dependency and next action.
3. **In Progress:** Before maintainer-directed or autonomous implementation,
   identify the actual implementer in the GitHub Assignee field, check whether
   another open PR already owns the change, and link the implementation PR to
   its issue. Human-led outside contributors may propose a change on an
   unclaimed issue without prior assignment or an AI-tool-specific approval;
   coordinate with existing assignees and follow the local contribution rules.
4. **In Review:** A PR is in review only when its scoped change and verification
   evidence are available. Check the required CI on the final PR head, test
   coverage appropriate to the risk, unresolved review findings and any
   repository-specific independent review or live-system validation. A bot
   review or passing CI cannot replace an outstanding product/safety decision.
5. **Done:** Record the actual disposition: a merged and validated change,
   a completed investigation with evidence, or an explained duplicate/not-
   planned closure. Use an issue-closing PR keyword only if merging actually
   satisfies its acceptance criteria. A merged PR is not proof that any
   deferred integration, fault, security or release verification has passed.

**Tracking without boards:** GitHub Issues and pull requests are the only
workflow tracker. The sole lifecycle label is `status: needs triage` for
unreviewed intake. Once triage is completed, remove that label after checking
the actual saved issue metadata; do not create `status: ready`, `status: in
progress`, or other duplicate workflow labels. Record the **Ready** decision,
remaining dependencies, and next action in the issue. For **In Progress**,
identify the actual implementer using Assignees and add a concise start/handoff
comment; link the working PR when it exists. **In Review** is visible from the
linked open, non-draft PR and its actual review/check status. A **Blocked**
issue records the blocker, responsible party, and unblock condition in a
comment. **Done** is the issue's Closed state with the recorded validated
outcome or explained disposition, not merely the existence of a merged PR.
Do not configure or depend on GitHub Projects/boards. Priority measures impact,
and size estimates effort; neither represents work-in-progress.
This guidance introduces no new automated merge gate and does not supersede
this repository's design, testing, security, or release requirements.
