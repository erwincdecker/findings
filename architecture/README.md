# Functional Architecture

**Status: DOCUMENTED CONCEPTS. Not a live topology or security inventory.**

The homelab is organized by operational responsibilities rather than by individual machines. Core layers include compute and availability, data and storage, user applications, security, integrations, and managed automation.

## Operating principles
- Separate source configuration, operational evidence, and publicly shareable lessons.
- Use the application's native APIs and lifecycle for normal behavior; add integration code only for missing functionality.
- Confirm actual runtime state before modifying resources.
- Prefer reversible, targeted changes with backup and independent health verification.
- Record uncertainty explicitly. A design, test, or commit is not proof of production completion.

Start with the [functional capability map](FUNCTIONAL_MAP.md).