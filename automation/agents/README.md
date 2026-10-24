# Agents and Task Orchestration

**Status: DESIGN / CAPABILITIES MUST BE LIVE VERIFIED.**

This area documents the architecture of bounded execution agents, program-level coordination and task routing.

## Functional separation
- **Program coordinator:** prioritizes work, identifies shared dependencies, checks reports and flags cross-project conflicts.
- **Execution agent:** performs only allowlisted capabilities within explicit scope.
- **Event bus:** transports immutable facts and commands with controlled privileges.
- **Review and quality function:** verifies evidence, completion and outstanding risks.

## Safety model
Use unique request IDs, pinned source revisions, scoped permissions, lease/replay protection, evidence publication and a policy for uncertain outcomes. Do not allow a status report or an event to become implicit authorization for arbitrary host commands.

A management highlight report should distinguish completed work, upcoming priorities, risks, blockers, constraints, impact and independent verification. Proposed autonomy is not proof of deployed autonomy.