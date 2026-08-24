# AGENTS.md

This file governs coding-agent work in `ocode-elixir-agentic`.

## Read first

Before changing implementation, read in this order:

1. `docs/planning/STATUS.md`
2. the explicit task/start prompt supplied by the operator
3. the relevant milestone/layer in `ROADMAP.md`
4. focused architecture/agent documentation referenced by that work

Do not load unrelated documentation merely because it exists.

## Authority

Repository planning documents describe intended work; they do not automatically authorize it.

Current implementation authority is recorded in `docs/planning/STATUS.md`.

A later explicit operator/start prompt may authorize a specific roadmap scope. When that happens:

1. treat the explicit prompt as authority only for the scope it names;
2. update `docs/planning/STATUS.md` before substantive implementation to record the active scope;
3. do not infer permission for later milestones from nearby roadmap text;
4. stop at any mandatory roadmap review gate even if additional work appears straightforward.

If authorities conflict, use the narrower safe interpretation and report the conflict rather than silently broadening scope.

## Engineering contract

- Repository state is evidence. Summaries are not.
- Inspect existing source, tests, configuration, and history relevant to the task before modifying them.
- Prefer small designs that protect the property at risk.
- Do not create placeholder abstractions, directories, processes, or interfaces only to satisfy a planned shape.
- Keep domain behavior separate from process/lifecycle ownership.
- Treat capability as distinct from authority: an installed framework or dependency does not get to redefine application boundaries.
- Side effects and nondeterminism should cross explicit boundaries when that improves determinism and testability.
- Do not hide business rules in GenServer callbacks merely because a process is involved.
- Do not use `Process.sleep/1` as proof of synchronization.
- Do not couple an interface directly to concrete adapters or persistence merely for convenience unless the accepted architecture explicitly permits it.
- Do not accept a green test suite as proof of an architectural property the tests do not exercise.

## Change workflow

For an authorized layer:

1. Inspect current repository truth.
2. Restate internally what must actually become true.
3. Identify existing contracts and dependencies consumed by the change.
4. Implement the smallest coherent slice.
5. Add or repair tests that prove the intended property.
6. Add negative/failure evidence where the roadmap requires rejection behavior.
7. Run the exact relevant validation commands.
8. Review the diff for scope drift and accidental coupling.
9. Commit bounded work with a descriptive commit message when the task authorizes commits.
10. Return the handoff defined in `docs/planning/HANDOFF.md`.

Do not continue into the next gated milestone simply because the current one passes.

## Validation semantics

Until M1 defines canonical Mix commands, use the actual validation available in the repository and report exactly what ran.

Once present, the stable meanings are intended to be:

- `mix doctor` — environment/toolchain validity
- `mix check.fast` — bounded inner-loop validation
- `mix check` — normal repository acceptance
- `mix check.ci` — expanded/static/release qualification
- `mix architecture.check` — architectural dependency validation

A command result is evidence only for what the command exercises.

## Definition of done

A work package is not complete merely because code exists or tests pass.

Completion requires:

- authorized scope implemented
- required acceptance properties demonstrated
- exact validation evidence reported
- required negative/failure evidence demonstrated
- architectural decisions surfaced
- anything unproven stated explicitly
- scope deviations stated explicitly
- repository left in a coherent state

## Forbidden completion shortcuts

Do not substitute these for evidence:

- "looks good"
- "should work"
- "all requirements handled"
- "tests pass" without the command/result
- an implementation summary written by the same agent
- successful compilation as proof of runtime semantics
- unit tests as proof of public-boundary acceptance

## Scope discipline

Do not introduce before roadmap authorization:

- Phoenix
- Ecto
- LiveView
- database dependencies
- background-job systems
- project generator machinery
- upgrade machinery
- AI orchestration/planning systems
- deployment-platform infrastructure

Dependencies may be researched when necessary to make an authorized decision, but research does not authorize implementation.

## Handoff

Every substantive implementation run must end using `docs/planning/HANDOFF.md` exactly enough that another reviewer can independently determine:

- what changed
- what was actually proven
- what remains unproven
- whether scope stayed bounded
- whether the next dependency is ready
