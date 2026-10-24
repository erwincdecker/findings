# Agent Program Management and Execution

**Status: PROPOSED AND DOCUMENTED CONTROLS; DEPLOYMENT REQUIRES SEPARATE PROOF.**

Separate a program manager from the agents executing individual tasks. The manager tracks priorities, dependencies, quality, blockers and meaningful highlights. The execution agent should accept only scoped, authenticated, replay-protected commands.

A healthy orchestration loop confirms that tasks are running, verifies outcome evidence and escalates ambiguous states rather than endlessly restarting uncertain operations. Report source-stage and live-pass work separately.

Do not publish real tokens, operational command queues, privileged SSH access paths or agent endpoint configuration.