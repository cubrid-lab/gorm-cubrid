# AGENTS.md

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

## Issue triage lifecycle

Every new issue in this repository starts with `status: needs triage`.
Issue forms mark it at creation; the `issues: opened` workflow covers other
human submissions. Agents creating issues using `GITHUB_TOKEN` must include
it explicitly because their own events may not retrigger the workflow.

1. Verify observed problem and evidence, unknowns, duplicates and open PRs,
   local ownership/type, scope/non-goals, acceptance and dependencies.
2. Inspect **all currently open issues in this repository** (all pages) and
   compare urgency, effect on users/data, existing priorities, active work and
   unresolved prerequisites rather than issue age.
3. Select exactly one `priority: critical/high/medium/low` and one original
   `size: XS/S/M/L/XL`, based on effort; split very large independent work
   while preserving issue ownership and accepted criteria.
4. Re-fetch the saved issue, check type, priority, size and duplicate decision
   against the actual labels, then remove `status: needs triage` only when
   this manual review is complete. Keep it for unresolved questions.

Do not automatically assign a priority or mark triage complete. Existing
code, tests, security and release safeguards are unaffected.
