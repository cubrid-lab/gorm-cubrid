## Summary

<!-- Brief description of changes -->

## Changes

<!-- List the specific changes made -->

-

## Type of Change

<!-- Check the relevant option -->

- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] New feature (non-breaking change that adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to change)
- [ ] Documentation update
- [ ] Refactoring (no functional changes)
- [ ] Chore (maintenance, dependencies, CI, etc.)

## Checklist

- [ ] My code follows the project's code style
- [ ] I have run `make check` (fmt + vet + test)
- [ ] I have added tests for new functionality (if applicable)
- [ ] I have updated documentation (if applicable)
- [ ] My changes do not introduce new warnings

## Related Issues

<!-- Link to related issues: Closes #123, Fixes #456 -->

## CUBRID correctness and regression evidence

<!-- For affected behavior, link reproducible evidence or state not yet tested. See docs/ENGINEERING_INVARIANTS.md. -->

- [ ] The ORM SQL and schema/migration behavior is verified against CUBRID rather than inferred from another dialect; unsupported clauses are not silently dropped.
- [ ] For relevant changes, I tested transaction commit/rollback/failure and result/value fidelity through the actual `cubrid-go` driver.
- [ ] I distinguished real DB integration from mocked/offline tests, reported actual tests run/skipped and marked unverified DB versions accurately.
- [ ] I reviewed GORM model/public API and module compatibility, including existing users of the in-driver dialector.
