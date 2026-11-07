# ADR 0001: Release governance and compatibility contract

- **Status:** Proposed
- **Date:** 2026-11-07
- **Decision scope:** gorm-cubrid release and supported-consumer contract
- **Owner:** Repository maintainers (approval not yet recorded)

## Context

The CUBRID Lab language drivers and ORM adapters progressed at different rates. An optimistic package number, borrowed ORM backend, passing mocks or a green but empty integration job must not accidentally imply production readiness. The Python driver and SQLAlchemy dialect established useful regression evidence, but release rules must follow each ecosystem's real package manager and compatibility obligations.

## Decision

Treat the GORM Dialector, clause compilation, migration behavior and schema contracts as this package's versioned API. Align a validated GORM v1 compatibility generation with a tested cubrid-go v1, but not matching patch/minor digits. A change to generated SQL, AutoMigrate or default values can be breaking without any Go signature change. Preserve users of cubrid-go/dialector via an explicit transition. Apply Go module Semantic Import Versioning, tested GORM/drivers and real CUBRID CRUD/migration release gates; no green release if a CUBRID service starts yet no tagged tests execute.

Applicable SemVer classification: a compatible bug/security fix is generally PATCH, an additive feature MINOR, and an incompatible public behavior or supported-environment change requires a reviewed breaking release (under current 0.x rules, commonly the next 0.y.0; at 1.x+ a MAJOR). Changes to SQL generation, data types, migrations or transactions can be compatibility-breaking without exported-type changes. A release candidate must have a reviewed version and notes, a final immutable commit, actual live CUBRID evidence when database behavior is claimed, and verified published artifacts. Keep omitted/failed checks visible; never invent approval or successful publication.

## Alternatives considered

- **Rejected:** Automatically version the GORM dialector together with the Go driver; treat successful go test without real SQL assertions as verification; silently remove old in-driver Dialector users.
- **Rejected:** One centrally synchronized release number and a generic Python-style policy for all languages; each distribution has separate SemVer, packaging and runtime constraints.
- **Rejected:** Treat an issue, green mock suite, GitHub tag or draft changelog as sufficient release evidence.

## Consequences and trade-offs

Adds a necessary independent integration release gate and downstream migration cost, avoids incorrect generated DDL or broken imports.

Maintainers must explicitly review the supported matrix and any runtime/API removal. Compatible fixes may be released independently when their own gates pass. Release automation can prepare PRs but must not bypass a maintainer review, exact candidate test or registry verification.

## Verification / acceptance criteria

- [ ] Maintainer reviews and accepts this ADR, links the approval and records acceptance date.
- [ ] Published README/release guide and dependency version declarations agree with the reviewed compatibility contract.
- [ ] A candidate-specific CI check identifies supported CUBRID server versions, tests run/passed/failed/skipped, runtime and driver/ORM versions, and actual artifact.
- [ ] Required release checks reject failed, missing, zero-case or skipped live tests, with scoped exceptions documented as unsupported/unverified—not counted as passes.
- [ ] User-visible breaking changes have accurate migration notes; correct author attribution in curated release notes comes from verified merged contributions.
- [ ] Existing immutable tags and published versions remain untouched by adopting this ADR.

## References

[release policy](../RELEASE_POLICY.md), [GORM API policy #40](https://github.com/cubrid-lab/gorm-cubrid/issues/40), [live-DB tests #43](https://github.com/cubrid-lab/gorm-cubrid/issues/43)

**This ADR does not update version numbers, create a release, authorize publishing, or claim any database test has passed.** Its status remains **Proposed** until explicit maintainer approval.
