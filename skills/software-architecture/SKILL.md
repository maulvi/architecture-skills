---
name: software-architecture
description: >-
  Decide and review software architecture using trade-off analysis instead of
  pattern fashion. Use when choosing or changing an architecture style
  (monolith, modular monolith, layered, service-based, microservices,
  event-driven, pipeline, microkernel, space-based), deciding monolith vs
  distributed, drawing service or module boundaries, defining "-ilities" /
  non-functional requirements, writing ADRs, running risk assessment, or
  reviewing a system for architectural smells (distributed monolith, shared
  database, entity trap, architecture sinkhole, ESB reuse, granularity errors).
  Also when asked "which architecture should we use" or "is this design good".
  Reference material: `reference/digest.md` (distilled from Fundamentals of
  Software Architecture, 2nd ed. — Richards & Ford).
---

# Software architecture

Architecture is trade-off analysis under context. There are no correct answers,
only expensive ones. Never hand back a style recommendation without the matrix
that produced it.

## Non-negotiable sequence

1. **Characteristics first.** Derive "-ilities" from domain concerns, not from
   taste. Cap the list at 7, force stakeholders to pick top 3.
   Decompose composites ("fast end-of-day pricing" = performance + availability +
   scalability + reliability + recoverability + auditability).
2. **Find the quanta.** An *architecture quantum* is the smallest independently
   deployable part; it scopes one set of characteristics. One set suffices →
   monolith. Differing sets → distributed, and the sets draw the boundaries.
3. **Then pick a style** as the easiest implementation path for those
   characteristics — never first.
4. **Record decisions** as ADRs (Title, Status, Context, Decision, Consequences,
   Compliance) and mechanize what matters as fitness functions.

## The three laws

1. Everything is a trade-off. If something looks free, you haven't found the
   trade-off yet (code reuse costs abstraction + low volatility). Re-run the
   analysis for every new context; weight criteria, don't tally them.
2. *Why* matters more than *how* — record the rationale or a successor will
   swap gRPC for REST and get timeouts.
3. Decisions sit on a spectrum, not a binary. Name the spectrum and what the
   choice depends on.

## Hard rules

- Coupling: tight inside a quantum, loose across quanta; loosen further as scope
  broadens. Reuse and decoupling are mutually exclusive — pick.
- Prefer duplication over coupling across bounded contexts. Never share a schema
  across more than ~5–6 services; never share custom libraries across contexts.
- Never span transactions across services — fix granularity first; saga only when
  characteristics force the split.
- API gateway = routing + cross-cutting concerns. No business logic, no
  orchestration.
- Name components by role/action. `Manager`/`Processor`/`Handler` = Entity Trap.
  Split when the role statement needs *and* / *also*, or when two parts need
  different characteristics.
- Measure the tail (max, p95), never just the mean. Set budgets where physics
  constrains (latency, K-weight).
- Assume all 11 distributed fallacies; quantify latency, bandwidth, transport
  cost, administrator count before going distributed.
- Default synchronous. Async where rates, reliability, or characteristics
  diverge — you buy responsiveness with certainty.
- Decide at the last responsible moment: when deferring costs more than deciding.
  Justify in business terms (cost, time to market, user satisfaction, strategic
  positioning) or don't decide.
- Fewest characteristics, least-worst architecture. "Generic architecture"
  supporting everything is the Vasa — it capsizes on launch.

## Style quick reference

| Style | Partition | Quantum | Wins | Costs |
|---|---|---|---|---|
| Layered | technical | 1 | cost, simplicity, tooling | elasticity/scale 1★, no FT, high MTTR, sinkhole |
| Modular monolith | domain | 1 | cost/simplicity/modularity 5★ | deploy 2★, test 2★, scale/elasticity/FT 1★ |
| Pipeline | technical | 1 | cost/simplicity/modularity 5★ | scale/elasticity/FT 1★, no mid-flow recovery |
| Microkernel | either | 1 | evolvability, per-client variation | scale/elasticity/FT 1★, volatile core, plug-in hell |
| Service-based | domain | ≥1 | 4★ agility/test/deploy/FT, simplest distributed | scale 3★, elasticity 2★, ≤12 services |
| Event-driven | domain | 1…many | evolvability 5★, perf/scale/FT 4★ | simplicity/test 2★, poison events, no workflow owner |
| Space-based | technical | excludes DB | elasticity/scale/perf 5★ | simplicity/test/cost 1★, collisions, pump bottleneck |
| Microservices | domain | many | deploy/test/FT/scale/elasticity | performance, simplicity, data consistency |
| Orchestration SOA | technical | 1 | integration hub only | canonical coupling, Accidental SOA |

## Review checklist (smells)

Architecture sinkhole (>20–30% pass-through calls) · distributed monolith ·
shared database across services · Entity Trap components · Grains of Sand
(services too fine) · stamp coupling (fat payloads) · poison events (derived
events looping) · ESB doing orchestration · no fitness functions · diagrams
without a key or relying on color · decisions with no recorded rationale ·
Ivory Tower architecture · generic architecture.

## Deliverable shape

Characteristics (ranked, with the domain concern each came from) → quanta →
style choice with the weighted matrix → ADRs to write → fitness functions to add
→ the trade-offs accepted, stated explicitly.

Full detail, per-chapter notes, formulas (cyclomatic complexity, LCOM,
abstractness/instability/distance, collision rate), and the antipattern catalogue:
`reference/digest.md`.
