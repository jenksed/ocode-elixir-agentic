# Project Status

## State

```text
phase: planning
implementation_authorized: false
active_milestone: none
active_layers: none
mandatory_stop: before implementation
next_planned_scope: M0 + M1
```

## Current authority

The repository is ready for project-start prompt design, but implementation has not yet been authorized.

The roadmap documents intended future work. It is not a standing instruction to execute that work.

## How implementation becomes authorized

A direct operator/start prompt may authorize a specific roadmap scope.

The first planned authorization is:

```text
M0 — Blueprint Charter
M1 — Agentic Elixir Core
```

When such a prompt is received, the executing agent must first update this file to record:

```text
phase: implementation
implementation_authorized: true
active_milestone: M0 + M1
active_layers: exactly the layers named by the prompt
mandatory_stop: after M1 handoff/review gate
```

If the prompt authorizes less, record the narrower scope.

Do not mark later milestones active unless the operator explicitly authorizes them after the required review/reconciliation gate.

## Current repository purpose

Until implementation is authorized, changes to `main` should be limited to planning authority, roadmap quality, agent operating rules, and other setup required to make the first implementation run well-defined.

No Elixir scaffold, Phoenix/Ecto dependency, capability implementation, generator, or speculative application code is currently authorized.

## Next decision

Develop and review the project-start prompt for the initial M0 + M1 implementation run.
