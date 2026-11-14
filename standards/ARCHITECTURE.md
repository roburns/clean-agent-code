# Architecture

Preserve intentional architecture and domain semantics. Choose complexity from
current requirements, not the popularity of Clean Architecture, hexagonal design,
domain-driven design, or microservices.

## Simplest sufficient solution

Among solutions that meet the requirement, choose the one with the least code and
fewest moving parts. Prefer removing or reusing existing code, the standard library,
or an approved dependency over writing new code. Do not add unrequested features,
options, parameters, layers, wrappers, or handling for states that cannot occur.
Shorter means fewer concepts, not denser code; keep clear names and readable flow.

Never simplify by removing security controls, validation at trust boundaries,
authorization, error handling callers rely on, or tests covering the change. When
the shortest version would weaken one of these, keep the control and simplify
elsewhere. When a requirement forces complexity, name the requirement.

Scale engineering depth to the project maturity recorded in project context: a
prototype warrants less test depth, error handling, and documentation than a
production service. Maturity never relaxes the security controls above.

## Design boundaries

Separate concerns into cohesive modules with explicit ownership and domain boundaries.
Keep dependencies directed toward stable contracts; avoid circular dependencies.
Encapsulate invariants and mutable state. Expose small interfaces with explicit
inputs, outputs, errors, and side effects. Keep coupling visible and controlled.
Use straightforward functional decomposition when it makes responsibilities clearer.
Prefer composition when behavior must vary independently; inheritance is appropriate
for a genuine substitutable relationship with a stable behavioral contract.

When the same logic serves several real data sources or providers, such as a live
database and a cache, keep that logic in one place and put the variation behind a
narrow boundary. Avoid both duplicating functions per source and branching on the
source inside every function. Keep independent mechanisms separate: pagination,
external batch limits, and concurrency are simpler as distinct steps than as one
combined loop.

Protect public contracts, including error behavior and serialization. Refactor in
small verifiable steps; preserve compatibility or document an approved migration.
Follow existing repository patterns unless evidence shows a concrete defect.

## Change impact

Before changing the name, signature, type, default, return value, error behavior,
side effects, or meaning of a variable, parameter, function, field, or
configuration key, find everything that depends on it. Do not rely on recall or on
the files already open: search each time, across everything the thing is visible
to. A local needs its function; an exported name needs the whole permitted
codebase.

Look for:

- Callers, implementers, overrides, and subclasses, including other packages and
  services in the repository.
- Tests, fixtures, and mocks that encode the old behavior.
- Uses a compiler cannot see: names in strings, reflection, templates, queries,
  serialized or persisted data, configuration, environment variables, build and CI
  scripts, generated code, and documentation.
- Behavior callers assume without stating it: units, ordering, nullability,
  defaults, mutation of arguments, and which errors can occur.

Update every dependent in the same change, or keep the old behavior compatible.
Treat a use outside the repository, or one the filesystem rules keep you from
inspecting, as a public contract. When the affected set is larger than the request
implies, stop and report it before editing.

Report with the change: what was searched and how, the dependents found, and any
left unchanged with the reason. A passing build or test run shows only that
covered code still works; it does not replace the search.

## SOLID as decision guidance

Single responsibility means cohesive reasons to change, not one method per class.
Open/closed design can stabilize a proven variation point without predicting every
future extension. Substitution includes invariants and error behavior, not merely
matching signatures. Interface segregation serves actual consumers. Dependency
inversion is useful when it reduces meaningful coupling or isolates external effects.
None requires an interface per class, a factory per dependency, or a dependency
injection container where direct construction suffices.

Before adding an abstraction, answer in review:

1. What problem does it solve?
2. Does the requirement exist today?
3. Could a simpler implementation satisfy it?
4. Does it reduce meaningful coupling?
5. Does it improve testability or ownership?
6. Does it align with this repository?
7. What maintenance burden does it introduce?

A payment-provider boundary justified by two supported providers and contract tests
can isolate meaningful variation. A plugin registry, factory, and interface around
one pure calculation with no variation requirement adds unjustified machinery.

## Evidence

Review dependency direction, owners, representative call paths, contract tests, and
the rationale for new layers. Use dependency-cycle tooling where available; tooling
cannot decide whether a domain boundary or abstraction is useful.
