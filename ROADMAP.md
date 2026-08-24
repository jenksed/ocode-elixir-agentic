# ocode-elixir-agentic Roadmap

## Objective

Build a reusable Elixir/OTP project blueprint that makes architecture, ownership, side effects, validation, and acceptance discoverable and enforceable for humans and coding agents.

Optional application capabilities such as Ecto, PostgreSQL, Phoenix, and LiveView must extend the core without redefining it. Profiles are proven combinations of capabilities. Project generation is deliberately late: first prove the architecture that will be generated.

## Roadmap state

- Current authority: **PLANNING ONLY**
- Implementation authorized: **NO**
- Planned first implementation scope: **M0 + M1**
- Mandatory stop after first implementation scope: **YES — review before M2**

`docs/planning/STATUS.md` is the current authority record. If roadmap text and current status differ about what may be implemented now, the narrower authority wins.

---

## Core model

### Core

Mandatory structure and contracts inherited by every generated project:

- OTP/application conventions
- architectural dependency rules
- proof/test model
- deterministic side-effect boundaries
- agent instructions
- validation commands
- development/release conventions

### Capability

An optional architectural extension declaring, as applicable:

- `requires`
- `provides`
- `conflicts`
- dependency changes
- configuration changes
- architecture effects
- validation effects
- generated/managed artifacts

Examples: `ecto`, `postgres`, `phoenix`, `liveview`, `telemetry`, `jobs`, `http`.

### Profile

A named, continuously qualified combination of Core and Capabilities. Profiles add convenience, not new architecture semantics.

Examples: `otp`, `api`, `web`, `liveview`.

### Generator

The mechanism that materializes a project from already-proven Core, Capabilities, and Profiles. The generator implements architecture; it does not define it.

---

# Execution rules

These rules apply to every milestone.

1. Repository state is authoritative. Never treat a roadmap claim as proof that implementation exists.
2. Follow dependency order unless a section explicitly identifies parallel-safe work.
3. Prefer the smallest implementation that proves the required property.
4. Do not create empty architectural ceremony or placeholder modules merely to match a tree.
5. Process ownership, public boundaries, side-effect boundaries, and architectural direction must be explicit.
6. Where a material rule can reasonably be checked mechanically, prefer executable validation over prose alone.
7. Important architectural validators require negative evidence proving forbidden states are rejected.
8. A passing command proves only the property that command actually exercises.
9. Do not claim a layer or milestone complete without exact validation evidence and explicit unproven items.
10. Keep commits bounded around coherent changes.
11. Do not silently broaden scope into generators, upgrade machinery, deployment platforms, AI orchestration, or application-specific infrastructure before authorization.
12. Work that consumes an unresolved shared contract is dependency-sensitive even if it touches different files.

---

# M0 — Blueprint Charter

## Goal

Lock the vocabulary, architecture direction, initial technology decisions, and non-goals before implementation grows around accidental assumptions.

## Deliverables

Create:

```text
docs/blueprint/charter.md
docs/blueprint/glossary.md
docs/architecture/principles.md
```

Define and justify these initial decisions:

- single supervised Mix application by default
- OTP-first architecture
- no umbrella-by-default structure
- Phoenix optional
- Ecto optional
- LiveView optional
- PostgreSQL as the first reference SQL capability
- behaviours for important external/nondeterministic boundaries
- ExUnit as executable test authority
- Gherkin as acceptance specification, not proof by itself
- Docker supported but not mandatory for local development
- `mix release` as the production packaging baseline

Explicit non-goals:

- not a Phoenix starter
- not a framework layered over Phoenix
- not mandatory DDD
- not an AI orchestration framework
- not a replacement for Mix conventions
- not container-only development

## Gate M0

M0 passes when Core, Capability, Profile, and Generator are unambiguous; the decisions above are documented; and later implementation can be reviewed against those decisions without depending on unwritten intent.

---

# M1 — Agentic Elixir Core

Depends on: M0

M1 must leave behind a useful standalone Elixir bootstrap even if optional capability work never begins.

## L1 — Runtime foundation

Create the real supervised Mix application and pin the supported toolchain.

Expected baseline:

```text
mix.exs
mix.lock
mise.toml
.formatter.exs
.credo.exs
config/
lib/
test/
```

Canonical conceptual structure:

```text
lib/
├── app.ex
└── app/
    ├── application.ex
    ├── domain/
    ├── services/
    ├── ports/
    ├── adapters/
    └── runtime/
```

Do not populate unused directories with meaningless modules. Add one intentionally small vertical example that demonstrates the architecture end to end.

Required baseline evidence:

```bash
mix deps.get
mix compile --warnings-as-errors
mix test
```

## L2 — OTP ownership model

Define the responsibility split:

```text
Domain      business rules and transformations
Services    application orchestration
Ports       contracts for external/nondeterministic behavior
Adapters    implementations of ports
Runtime     process ownership and lifecycle
Supervisor  restart relationships
GenServer   stateful process behavior only when process semantics require it
```

Required conventions:

- business logic does not automatically belong in GenServers
- process ownership must be identifiable
- restart semantics must be intentional
- avoid arbitrary global process naming
- `Process.sleep/1` is not synchronization evidence

Provide executable examples/tests for normal startup, shutdown, child failure, and relevant restart behavior.

Acceptance property: every long-running reference process has identifiable ownership and restart semantics.

## L3 — Architectural dependency contract

Establish the default direction:

```text
Interfaces
    |
    v
Services ------> Domain
    |
    v
Ports
    ^
    |
Adapters

Runtime owns lifecycle; it is not the business API.
```

Initial allowed direction:

```text
Domain -> Domain
Services -> Domain + Ports
Adapters -> Ports + permitted external libraries
Interfaces -> Services + appropriate Domain values
Runtime -> components required for lifecycle ownership
```

Initial forbidden examples:

```text
Domain -> Phoenix
Domain -> Ecto
Domain -> concrete filesystem/network/environment implementation
Domain -> Adapter
Interface -> concrete Adapter
Interface -> Repo
```

Implement an executable architecture validator, preferably reachable as:

```bash
mix architecture.check
```

Add at least one deliberate invalid fixture or equivalent negative test.

Acceptance evidence must prove both a valid graph passing and a forbidden edge failing.

## L4 — Deterministic side-effect boundaries

Introduce explicit behaviours for a small set of representative nondeterministic/external operations. Good candidates include Clock, ID generation, HTTP, or filesystem access. Do not create every conceivable port.

Reference pattern:

```elixir
defmodule App.Ports.Clock do
  @callback now() :: DateTime.t()
end
```

Provide a real adapter and a deterministic testing approach such as Mox or explicit test implementations.

Core property: domain behavior receives nondeterministic values or crosses a declared contract; it does not silently obtain them from infrastructure.

Calls requiring scrutiny inside domain code include `DateTime.utc_now/0`, `System.get_env/1`, `File.*`, concrete HTTP clients, `Repo.*`, and `:rand.*`.

Acceptance evidence:

- domain tests require no database
- domain tests require no network
- domain tests require no external runtime configuration
- declared adapters satisfy their behaviours

## L5 — Proof taxonomy

Establish the testing model before optional frameworks multiply.

```text
test/
├── unit/
├── contract/
├── property/
├── integration/
├── acceptance/
└── support/
features/
```

Definitions:

- **unit** — isolated deterministic behavior
- **contract** — multiple implementations of a boundary satisfy the same expectations
- **property** — invariants exercised across generated inputs
- **integration** — real framework/infrastructure boundary interaction
- **acceptance** — behavior proven through a public application boundary
- **Gherkin** — acceptance specification; not executable proof unless explicitly wired to execution

Use StreamData for the reference property test. Provide at least one meaningful executable example for every proof category that is applicable to the core and map at least one `.feature` specification to acceptance evidence.

## L6 — Agent operating contract

Create a bounded root `AGENTS.md` and focused supporting context under `docs/agents/`.

The root contract should contain only universal rules:

- repository map
- authority model
- architecture direction
- change workflow
- validation semantics
- definition of done
- forbidden shortcuts
- pointers to deeper context

Focused documentation may include:

```text
docs/agents/architecture.md
docs/agents/otp.md
docs/agents/testing.md
docs/agents/dependencies.md
docs/agents/persistence.md
docs/agents/web.md
```

Capability-specific guidance must not pollute universal context. Until persistence/web capabilities exist, those documents should describe absence and future extension rules, not pretend implementations exist.

Acceptance property: an agent can identify where a change belongs, what constraints apply, and how to validate it without loading the entire repository documentation corpus.

## L7 — Validation control plane

Create stable operator commands:

```bash
mix setup
mix doctor
mix check.fast
mix check
mix check.ci
```

Target semantics:

### `mix setup`

Prepare dependencies and deterministic local project initialization.

### `mix doctor`

Validate environment/toolchain assumptions and report actionable failures.

### `mix check.fast`

Inner-loop validation: formatting, compile warnings, Credo, and fast/relevant tests.

### `mix check`

Repository acceptance baseline: formatting, warnings-as-errors compilation, Credo, complete normal tests, and architecture validation.

### `mix check.ci`

Expanded qualification: `mix check`, Dialyzer, dependency/security audit, and other deterministic expensive checks.

Machine-readable output for doctor/check is desirable if it can be implemented without disproportionate machinery. It must not weaken the primary command semantics.

## Gate M1

Demonstrate:

```text
fresh clone -> setup -> doctor -> check -> PASS
intentional architecture violation -> check -> FAIL
intentional test defect -> check -> FAIL
intentional environment defect -> doctor -> actionable FAIL
```

### Mandatory stop after M1

After M1 validation, stop implementation and return the required handoff evidence. Review and reconciliation are required before M2 is authorized. A green suite does not authorize M2.

---

# M2 — Reproducibility and Capability Substrate

Depends on: accepted M1

## L8 — Docker development environment

Add `Dockerfile`, `docker/dev.Dockerfile`, `compose.yaml`, and `.dockerignore`.

Both paths must prove the same repository properties:

```bash
mix setup
mix check
```

```bash
docker compose run --rm app mix check
```

Docker reproduces the project environment; it does not become a second source of architecture/configuration truth.

## L9 — Production release baseline

Prove a plain OTP release before Phoenix exists:

```bash
MIX_ENV=prod mix release
```

Validate production compilation, runtime configuration, startup, graceful shutdown, useful failure for missing required configuration, and version discoverability. Future capabilities must preserve release viability.

## L10 — Capability contract

Define the minimum representation for:

- id
- requires
- provides
- conflicts
- dependencies
- configuration effects
- architecture effects
- validation effects
- managed/generated artifacts

Do not lock the storage format prematurely.

## L11 — Transactional capability application

Required semantic flow:

```text
resolve
-> validate preconditions
-> stage changes
-> apply
-> format
-> compile
-> capability validation
-> promote
```

Failure must leave the previously accepted repository unchanged or deterministically restored.

Acceptance property: capability application either produces a valid repository or does not mutate accepted state.

## L12 — Capability dependency resolution

Reference graph:

```text
core
ecto -> core
postgres -> ecto
phoenix -> core
liveview -> phoenix
```

Recommended behavior: requesting `liveview` resolves `phoenix` transitively. Explicit conflicts stop application.

Before any major real framework capability, prove the complete mechanism with a small reference/fake capability including install, transitive dependency handling, rejection, rollback, and validation extension.

## Gate M2

Capability composition machinery is proven independently of Phoenix, Ecto, or another major framework.

---

# M3 — Foundation Capabilities

Depends on: M2

Once the capability contract is stable, Ecto, Phoenix, and Telemetry are potentially parallel-safe. PostgreSQL consumes Ecto and is not parallel-safe against unresolved Ecto contracts.

## L13 — Ecto

Requires: Core.

Ecto must not require Phoenix or PostgreSQL. Add persistence conventions, Repo boundary conventions, migrations where applicable, and testing support.

Default architectural constraints: Domain and interfaces do not depend directly on Repo. Do not force heavyweight domain/persistence separation when a simple data-centric application does not need it.

## L14 — PostgreSQL

Requires: Ecto.

Add the PostgreSQL adapter, database configuration, Docker service, SQL Sandbox, migration validation, test lifecycle support, and release configuration.

Prove creation from empty state, migrations, isolation, concurrency behavior where supported, unavailable-database behavior, and release configuration.

Capabilities extend existing setup/doctor/check commands rather than inventing separate operator workflows.

## L15 — Phoenix

Requires: Core. Phoenix must not require Ecto.

Add the web interface structure, endpoint, router, basic health route, request-boundary test, public application-boundary example, and production release endpoint proof.

Architectural direction:

```text
AppWeb -> Services
AppWeb -> appropriate Domain values
AppWeb !-> concrete Adapter
AppWeb !-> Repo
```

Phoenix must extend doctor, check, CI, Docker where required, and release validation.

## L16 — Telemetry

Requires: Core.

Define stable event conventions such as operation start/stop/exception with bounded metadata, no secrets, and no default high-cardinality identifiers. Event semantics must remain vendor-independent.

## Gate M3

Each foundation capability works independently where intended, declares its effects, extends validation correctly, preserves release viability, and composes through the capability substrate.

---

# M4 — Higher-Level Capabilities

Depends on relevant M3 capabilities.

## L17 — LiveView

Requires: Phoenix. Must not inherently require Ecto.

Add LiveView support, LiveView testing, UI composition conventions, and focused agent guidance.

Suggested UI structure:

```text
components/
├── primitives/
├── compositions/
└── features/
```

State ownership guidance:

```text
presentation/session UI state -> LiveView/component when appropriate
shared process state -> supervised runtime
durable business state -> domain/persistence
business rules -> domain/services
```

Default forbidden direction: LiveView to Repo or concrete external adapters.

Reference vertical proof: user event -> LiveView -> public application/service boundary -> domain behavior -> result -> rendered state.

## L18 — Background jobs

Select the concrete implementation only when this layer starts. If Oban is selected, declare its actual Ecto/PostgreSQL requirements.

Reference job contracts must identify input, ownership, idempotency expectations, retry semantics, terminal failure behavior, and observable evidence.

## L19 — External HTTP

Provide a standardized external HTTP port and adapter, likely using Req. Establish timeouts, retry rules, normalized external errors, and deterministic tests.

Rules: do not scatter concrete HTTP calls through application code; normalize external response shapes at the adapter boundary; retries must respect safety/idempotency.

## L20 — Concurrency reference pack

Provide executable reference scenarios for GenServer lifecycle, DynamicSupervisor, Registry, Task.Supervisor, monitoring/linking, timeouts, reconstruction/restart, deterministic synchronization, and common races.

This may eventually become a recipe rather than an installed capability. Do not invent a recipe abstraction until implementation pressure proves it is needed.

---

# M5 — Profiles and Composition Qualification

Depends on stable capabilities.

## L21 — OTP profile

Core only. Target workers, daemons, CLI applications, and process-oriented services.

## L22 — API profile

Baseline: Core + Phoenix + Telemetry. Persistence remains independently selectable.

## L23 — Web profile

Expected baseline: Core + Phoenix + Ecto + PostgreSQL + Telemetry. Final defaults must be justified by qualification evidence, not convenience alone.

## L24 — LiveView profile

Expected baseline: Core + Phoenix + LiveView + Ecto + PostgreSQL + Telemetry.

## L25 — Profile matrix qualification

Every supported profile must be materialized and validated in CI. Prove setup, doctor, formatting, warnings-as-errors compilation, architecture checks, tests, static analysis, security/dependency checks, release build, and boot smoke test.

A graph that resolves is not automatically a supported profile.

---

# M6 — Deterministic Agent Tooling

Depends on stable architecture and capability semantics.

Do not build an AI planner into the bootstrap.

## L26 — Repository inspection

Provide a command such as `mix agent.inspect` that exposes project identity, installed capabilities, profile where applicable, validation commands, and architecture/version metadata. Prefer machine-readable output.

## L27 — Context compilation

Provide bounded context by work domain, e.g.:

```bash
mix agent.context domain
mix agent.context runtime
mix agent.context persistence
mix agent.context web
mix agent.context liveview
```

Output may include relevant rules, files, contracts, validation commands, forbidden dependencies, and applicable docs. Do not implement context compilation as indiscriminate document concatenation.

## L28 — Deterministic change qualification

A command such as `mix agent.plan` may inspect changed paths, identify affected capabilities, required validation, and relevant architecture context. It must not claim semantic correctness from filenames or diffs alone.

---

# M7 — Generator

Depends on qualified profiles and stable capability semantics.

## L29 — Generator core

Create projects outside an existing Mix project. Potential final interface:

```bash
ocode-elixir new my_app
```

Initial generation supports Core only.

## L30 — Capability selection

Examples:

```bash
ocode-elixir new my_app --with phoenix
ocode-elixir new my_app --with phoenix,liveview
ocode-elixir new my_app --with ecto,postgres
```

Use the established dependency resolver.

## L31 — Profile selection

Examples:

```bash
ocode-elixir new my_app --profile otp
ocode-elixir new my_app --profile api
ocode-elixir new my_app --profile web
ocode-elixir new my_app --profile liveview
```

Overrides are permitted only when the resulting graph remains valid.

## L32 — Post-generation verification

Generation ends only after validation. Report resolved capabilities, materialization result, formatting, compilation, tests, architecture checks, and anything unavailable/unverified. Never report successful generation when required validation failed.

---

# M8 — Hardening and Qualification

Depends on working generated projects.

## L33 — Blueprint metadata

Generated repositories record enough metadata to support future inspection and migration, e.g. `.blueprint/manifest` and `.blueprint/version`. Metadata does not itself authorize upgrade support.

## L34 — Generated-file ownership

Define ownership classes before implementing upgrades:

- blueprint-owned
- user-owned
- shared

Capability metadata must be able to express ownership where necessary.

## L35 — Upgrade model

Only after real generated projects exist. Any future `ocode-elixir upgrade` must account for blueprint version, capability versions, migration sequencing, file ownership, local modifications, conflict detection, and rollback.

Upgrade support is not P0.

## L36 — Real-project qualification

Before stable release, build at least:

1. one pure OTP/service project
2. one Phoenix API project
3. one LiveView project

Evaluate actual agent navigation, context cost, architecture violations attempted, test selection, validation speed, unnecessary boilerplate, repeated misunderstandings, documentation actually consumed, capability coupling, and operational friction.

Change the blueprint based on demonstrated failure patterns.

---

# Dependency summary

```text
M0 Blueprint Charter
        |
        v
M1 Agentic Elixir Core
        |
        v
MANDATORY REVIEW / RECONCILIATION
        |
        v
M2 Reproducibility + Capability Substrate
        |
        v
M3 Foundation Capabilities
        |
        v
M4 Higher-Level Capabilities
        |
        v
M5 Profiles + Matrix Qualification
        |
        v
M6 Deterministic Agent Tooling
        |
        v
M7 Generator
        |
        v
M8 Hardening + Real-Project Qualification
```

# Parallelization rules

Before M2, assume work is dependency-sensitive unless independently demonstrated otherwise.

After M2 stabilizes the capability contract:

```text
Ecto      -- potentially parallel-safe
Phoenix   -- potentially parallel-safe
Telemetry -- potentially parallel-safe
```

PostgreSQL consumes Ecto. LiveView consumes Phoenix. Profiles consume stable capabilities. Generator work consumes stable profile/capability semantics.

Different files do not imply independent work.

# Layer completion evidence

Every completed layer or assigned work package must return:

```text
MILESTONE:
LAYER:

CLAIMS:
- implemented behavior

CHANGED FILES:
- exact paths

VALIDATION:
- exact command
- PASS | FAIL
- relevant result

NEGATIVE / FAILURE EVIDENCE:
- invalid cases exercised
- expected rejection observed

ARCHITECTURAL DECISIONS:
- decisions introduced or changed

UNPROVEN:
- properties not demonstrated

SCOPE DEVIATIONS:
- work outside authorized scope

NEXT DEPENDENCY:
- what must be true before subsequent work may begin
```

Statements such as "tests look good", "should work", or "requirements complete" are not substitutes for evidence.
