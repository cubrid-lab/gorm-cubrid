# AGENTS.md

## Organization-wide issue and PR standards

See [the cubrid-lab standard](https://github.com/cubrid-lab/.github/blob/main/docs/ISSUE_PR_COMMIT_STANDARD.md). Use clear type-only **Issue** titles (`feat:`, `bug:`, `docs:`), no required module scope. State problem/goal and verifiable completion conditions, distinguishing evidence from assumptions. Triage establishes validity, duplicates, priority and size; **PRs and commits** keep their existing Conventional Commits and CI checks. Avoid AI-agent narration, personal references and opaque internal tracking codes in issue prose.


## Purpose
`gorm-cubrid` is a GORM dialector and driver integration for CUBRID.

## Read First
- `README.md`
- `CONTRIBUTING.md`
- `docs/agent-playbook.md`

## Working Rules
- Preserve GORM conventions and keep examples aligned with actual dialect behavior.
- Minimize breaking changes to migrator, clause, and DSN behavior.
- Update docs when data type mappings or supported features change.
- Add tests for dialect behavior changes, especially schema and SQL generation.

## Development Workflow (cubrid-lab org standard)

Use **one review policy** for human-authored contributions and all coding
tools (OpenCode, Codex, Claude Code, ChatGPT, Copilot, and others).
No Oracle pre/post review or particular AI reviewer is required by tool.

1. **Implement:** scope a focused change, add relevant tests, and update
   affected documentation in the same PR (or explain why docs are unaffected).
2. **GitHub CI:** required GitHub Actions checks on the final PR head are
   authoritative. Run applicable language-specific lint, tests, documentation,
   integration and security checks. Local runs are encouraged, not a separate
   prerequisite for merging.
3. **Review:** check correctness, risk, tests, security and maintainability.
   Oracle, Copilot and `agent-review` are optional **advisory** evidence.
4. **Merge:** address or disposition findings, verify required checks, and
   have the responsible maintainer decide on merge. Security-sensitive
   CI permissions, authentication and publishing changes require an
   independent maintainer's assessment.

Preserve existing branch protections, CI gates, integration tests, releases
and protected security paths. No automatic approval follows from green CI or
AI summaries.

## Validation
- `go test ./...`
- `make vet`
- `make test-integration` when integration paths are touched
