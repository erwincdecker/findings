# Engineering Quality, Testing and Release Gates

**Status: GENERAL QUALITY PRACTICES.**

## Coverage
Static checks, unit tests, API contract tests, integration tests, end-to-end flows, visual regression, accessibility, security review and rollback validation answer different questions.

## Lessons
- Check the rendered result, not merely whether a build succeeded.
- Use deterministic fixtures and a reproducible environment.
- Separate staging experiments from production mutation.
- An import, upgrade or migration is complete only when all related functional outcomes are verified.
- Capture errors as actionable reasons, not generic pass/fail labels.
- Regression tests must protect adjacent components from unexpected shared changes.

## Change lifecycle
Baseline → scope → isolated test → review → controlled deployment → observed behavior → documentation → independent closure.

A green state should only be claimed for the evidence actually collected.