# ocode-elixir-agentic

An Elixir/OTP project blueprint designed for reliable development by humans and coding agents.

The project will establish a small, enforceable Elixir core first, then add optional capabilities such as Ecto, PostgreSQL, Phoenix, LiveView, telemetry, jobs, and external HTTP without allowing those capabilities to redefine the core architecture.

## Current state

**Planning complete enough to begin prompt design. Implementation is not yet authorized.**

The repository currently contains the governing roadmap and execution contract only. The next step is to develop the initial project-start prompt that will authorize M0 and M1 work.

Do not infer implementation authorization from the existence of later roadmap milestones.

## Read order for coding agents

1. `AGENTS.md` — governing execution and evidence rules.
2. `docs/planning/STATUS.md` — current authority and stop condition.
3. `ROADMAP.md` — ordered architecture and implementation roadmap.
4. `docs/planning/HANDOFF.md` — required completion/handoff contract.

## Intended architecture

Every generated project starts from a supervised Elixir/OTP core. Optional features are modeled as capabilities with explicit dependencies, architectural effects, validation effects, and composition rules. Profiles are proven combinations of capabilities. A generator comes only after the architecture and capability model have been qualified.

The default design priorities are:

- OTP ownership and lifecycle are explicit.
- Domain behavior is separated from runtime/process ownership.
- Nondeterministic and external effects cross declared boundaries.
- Architecture rules are executable where practical.
- Validation commands have stable meanings.
- Tests are evidence for specific properties, not generic completion tokens.
- Agent context stays bounded and task-relevant.
- Optional framework choices do not become accidental architecture.

## Repository

`git@github.com:jenksed/ocode-elixir-agentic.git`
