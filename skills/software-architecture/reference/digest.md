# Fundamentals of Software Architecture (2nd ed.) — Digest

Richards & Ford. 27 chapters, 3 parts: foundations, architecture styles, techniques/soft skills.
Core thesis: **architecture is trade-off analysis under context**. No correct answers, only expensive ones.

## 1. Definition and the three laws

Architecture = **structure** + four dimensions:
1. **Architecture style** (starting topology)
2. **Architecture characteristics** ("-ilities" = capabilities; what the system must *do* beyond domain behavior)
3. **Logical components** (= behavior: domains, entities, workflows)
4. **Architecture decisions** (= constraints/rules for construction)

**Law 1: Everything in software architecture is a trade-off.**
- Corollary 1: if something looks like it isn't a trade-off, you haven't found the trade-off yet. (Code reuse needs *abstraction* + *low volatility* — that's the hidden cost.)
- Corollary 2: you can't do trade-off analysis once. Every context re-weights the same matrix.

**Law 2: Why is more important than how.** The analysis context (why) is lost unless recorded → ADRs + diagrams.

**Law 3: Most decisions aren't binary; they sit on a spectrum between extremes.** State the spectrum and what it depends on.

Consequences of Law 1: you cannot have high decoupling *and* high reuse (reuse is implemented via coupling). Pick.

## 2. Architecture vs design

Spectrum, decided by three criteria:
- **Strategic vs tactical** (weeks of planning + many stakeholders + long-lived → architecture)
- **Effort to change** (Fowler: "the stuff that's hard to change")
- **Significance of trade-offs** (microservices-level trade-offs → architecture; splitting a class → design)

**Architect's asset is technical breadth, not depth.** Knowledge pyramid: *know* / *know you don't know* / *don't know you don't know*. Breadth = how deep the middle layer penetrates the bottom. Depth atrophies on purpose.
- **20-minute rule**: 20 min/day, first thing, *before* email.
- **Personal technology radar**: quadrants tools/languages+frameworks/techniques/platforms; rings Hold/Assess/Trial/Adopt.
- **Frozen Caveman antipattern**: architect reverts to a pet irrational concern ("but what if we lose Italy?").

**Role expectations**: make architecture decisions; continually analyze the architecture; keep current with trends; ensure compliance; have breadth across technologies; know the business domain; lead teams (interpersonal skills); navigate politics. **Leadership is at least half the job**; nearly every decision will be challenged.

**Guide, don't specify**: prefer "use a reactive-based framework" over "use React.js". Name a concrete technology only when a characteristic demands it (scalability, performance, availability).

**Architecture vitality** = whether an architecture defined 3+ years ago still fits the business/technology; **structural decay** = code changes eroding required characteristics. Test and release environments count too: weeks to test plus months to release means no agility, whatever the diagram says.

**Bottleneck Trap**: the architect who owns critical-path code blocks the whole team. Instead code a minor business feature one to three iterations out. Stay hands-on via production-quality POCs, technical debt, bug fixes, automation/fitness functions, code review.

## 3. Modularity

Modularity = logical grouping of related code (not physical separation). **Beware granularity** — granularity is where architectures die (Spaghetti, Distributed Monolith, Big Ball of Distributed Mud).

**Cohesion**, best → worst: functional, sequential, communicational, procedural, temporal, logical, coincidental. Metric: **LCOM** (lack of cohesion in methods = sum of method sets not sharing fields). LCOM finds *structural* incohesion only — not whether things belong together logically (Law 2).

**Coupling**: **afferent** (Ca, incoming) vs **efferent** (Ce, outgoing).
- **Abstractness** A = abstract/(abstract+concrete)
- **Instability** I = Ce/(Ce+Ca)
- **Distance from the main sequence** D = |A + I − 1|; far upper-right = **Zone of Uselessness** (too abstract), far lower-left = **Zone of Pain** (too concrete/brittle).

**Connascence** (Page-Jones) — coupling that includes degree and locality:
- Static: name, type, meaning (magic numbers), position (arg order), algorithm.
- Dynamic: execution (order), timing (race conditions), values (must change together — distributed transactions), identity (must reference same entity).
- Three properties: **strength** (how hard to refactor — static is weaker/easier), **locality** (proximal code tolerates stronger forms), **degree** (size of the blast radius).
- Page-Jones guidelines: minimize overall connascence via encapsulation; minimize connascence *crossing* encapsulation boundaries; maximize connascence *within* boundaries.
- Weirich: **Rule of Degree** — convert strong forms into weaker ones. **Rule of Locality** — as distance between elements grows, use weaker forms.

## 4. Architecture characteristics

A requirement is an architecture characteristic only if it: (1) specifies a **non-domain** design consideration, (2) **influences structure**, (3) is **critical to success**.
- **Implicit** (rarely in requirements: availability, security, reliability, modularity) vs **explicit**.
- Categories: **operational** (availability, continuity, performance, recoverability, reliability/safety, robustness, scalability, elasticity), **structural** (configurability, extensibility, installability, leverageability, localization, maintainability, portability, upgradeability), **cloud** (on-demand scalability/elasticity, zone-based availability, region-based privacy), **cross-cutting** (accessibility, archivability, authentication, authorization, legal, privacy, security, supportability).
- **Scalability ≠ elasticity**: scalability = handles growth over time; elasticity = handles bursts.
- **Composite characteristics must be decomposed** ("fast end-of-day pricing" = performance + availability + scalability + reliability + recoverability + auditability). Decomposition creates the **ubiquitous language**.
- **Translate domain concerns**: M&A → interoperability, scalability, adaptability, extensibility. Time to market → agility, testability, deployability. User satisfaction → performance, availability, fault tolerance, testability, deployability, agility, security. Time budget → simplicity, feasibility.
- **Fewest possible characteristics.** Generic architecture / overspecification is an antipattern (the *Vasa*: king wanted transport + gunship, one deck became two, ship capsized on launch).
- **Worksheet method**: ≤7 candidate characteristics (implicit ones pulled into column 1, displaced items to "Others Considered"), then stakeholders pick top **3**, in any order. A full ranked priority list is a fool's errand.
- You cannot determine the "least important" characteristic — that's the point. ISO publishes an overlapping, incomplete list; the authors exclude functional suitability (it's the motivation, not a characteristic).
- Katas (Neward): description, users, requirements, additional context; timeboxed team design + vote.
- Never decide in isolation from the implementation team — the **Ivory Tower** antipattern.

**Scope: the quantum.** An **architecture quantum** = smallest independently deployable part of the system; it defines the scope for one set of characteristics. Features: independent deployment, high functional cohesion, low external static coupling, synchronous calls to other quanta.
- One characteristic set may suffice → monolith. Differing sets → distributed, and the sets *draw* the quantum boundaries.
- Coupling taxonomy: **semantic** (inherent to the problem — can't design away), **implementation**, **static** (wiring), **dynamic** (runtime). Rule: **the broader the scope, the looser the coupling must be.**
- Sync calls merge quanta (dynamic quantum entanglement). Async preserves boundaries but costs determinism.

## 5. Measuring and governing

Metrics per class of characteristic:
- **Operational**: measure max, not just averages (1% of requests 10× slower hides in the mean). Statistical models of expected behavior; alarm on deviation. Budgets: first contentful paint, first CPU idle, K-weight page budgets.
- **Structural**: **cyclomatic complexity** CC = E − N + 2P (no decisions → 1). Industry acceptable < 10; authors prefer < 5. CC > 50 can't be rescued by test coverage. CC can't separate **essential** from **accidental** complexity.
- **Process**: testability = coverage (100% coverage with weak assertions proves nothing); deployability = % successful deploys, duration, bugs introduced.

**Fitness function**: "any mechanism that provides an objective integrity assessment of an architecture characteristic or combination of characteristics." Not a framework — a *perspective* on existing tools (metrics, monitors, unit-test libs, chaos engineering). Guards the important-but-not-urgent.
- Examples: JDepend cycle test (fail build on cycles), distance-from-main-sequence threshold, ArchUnit/NetArchTest/PyTestArch/TSArch rules, Netflix Chaos/Latency/Security/Conformity monkeys.
- Characteristics of good ones: objective, automatable, explainable to developers, hard to game (coverage without assertions is gaming). Treat them as **checklists**, not gatekeeping.

## 6. Components and partitioning

**Top-level partitioning** is the defining decision:
- **Technical** (layered): easy to find code by capability; but domain concepts smear across every layer (`CatalogCheckout` in each layer).
- **Domain** (modular monolith, DDD): workflow-aligned; but customization code duplicates across domains. Industry trend: domain.

Component identification is an iterative loop: identify candidates → assign user stories → analyze roles/responsibilities → analyze characteristics → restructure. Never perfect the first pass (you know least then).

Two techniques: **workflow** (happy-path steps) and **actor/action** (includes the system as an actor; yields more components).
- **Entity Trap**: components named `XManager`/`Supervisor`/`Controller`/`Handler`/`Engine`/`Processor` — ambiguous role, dumping ground, too coarse. Pure CRUD needs a CRUD framework, not an architecture.
- **Cohesion test**: if the role statement needs *and* / *also* / *as well as*, split it.
- **Characteristic analysis overrides functional cohesion**: split `Bid Capture` into `Bid Capture` + `Auctioneer Capture` because bidders need scalability/elasticity and the auctioneer needs reliability/availability.
- **Law of Demeter**: reduces *knowledge*, not responsibility; it **redistributes** coupling rather than removing it.
- No one true design — pick the **least worst** set of trade-offs.

**Conway's Law**: designs copy the organization's communication structures. **Inverse Conway maneuver** / team topologies: shape teams to get the architecture you want. Team types: stream-aligned, enabling, complicated-subsystem, platform (self-service internal product).

**Eight fallacies of distributed computing** (Deutsch) + the authors' three:
1. Network is reliable 2. Latency is zero 3. Bandwidth is infinite 4. Network is secure 5. Topology never changes 6. One administrator 7. Transport cost is zero 8. Network is homogeneous
9. Versioning is easy 10. Compensating updates always work 11. Observability is optional

Quantify before distributing: average *and* p95/p99 latency (avg 60 ms, p95 400 ms; 100 ms × chained calls = 1 s), bandwidth (**stamp coupling**: 500 KB × 2,000 req/s = 1 GB/s for 200 bytes of need), transport dollars, how many administrators actually exist.

## 7. Architecture styles

Ratings tables in the book are star charts; here they are in prose form, with the numbers the text states.

| Style | Partitioning | Quantum | Strong at | Weak at / costs |
|---|---|---|---|---|
| **Layered (n-tiered)** | technical | 1 | cost, simplicity, familiarity; best governance tooling | elasticity & scalability 1★, testability low, deployability low, no fault tolerance, high MTTR (2–15+ min startup), Architecture Sinkhole |
| **Modular monolith** | domain | 1 | cost, simplicity, modularity (5★); domain alignment without ops cost | deployability 2★, testability 2★, elasticity/scalability/FT/availability 1★; unstructured monolith, JAR/DLL hell, mediator coupling |
| **Pipeline** | technical | 1 | cost, simplicity, modularity 5★; deterministic ordered steps | elasticity/scalability/FT 1★; error recovery mid-pipeline, bidirectional needs, overloaded filters |
| **Microkernel** | technical or domain | 1 | cost/simplicity 5★, evolvability, incremental feature add/remove | scalability/elasticity/FT 1★; volatile core, plug-in dependency hell, contract testing across plug-in versions |
| **Service-based** | domain | ≥1 | pragmatic middle: four stars across agility/testability/deployability/FT/availability; simplicity relative to other distributed styles | scalability 3★, elasticity 2★; ≤12 domain services or testing/deploy/DB-connection chaos; coarse-grained |
| **Event-driven** | domain | 1…many | evolvability 5★, performance/scalability/FT 4★, async responsiveness | simplicity/testability 2★; nondeterminism, poison events, workflow state ownership, recoverability nearly infeasible |
| **Space-based** | technical | excludes DB | elasticity/scalability/performance 5★; near-infinite under unpredictable spikes | simplicity/testability/cost/team elasticity 1★; data collisions, data-pump bottlenecks, memory ceiling |
| **Orchestration-driven SOA** | technical | 1 despite distribution | (historical) enterprise reuse, integration hub | worst deployability/testability; canonical-model coupling, bureaucratic ESB, "Accidental SOA" |
| **Microservices** | domain | many | deployability, testability, FT, scalability, elasticity, evolvability | performance (network hops, per-endpoint security), simplicity, data consistency; granularity errors |

Per-style notes worth keeping:

**Layered** — result of *Architecture by Implication*; often unnecessary object instantiation for pass-through layers. **80/20 rule** for the Architecture Sinkhole: 20% sinkhole requests are fine, 80% means the style is wrong (fix: make all layers open, at the cost of harder change management). Excellent fitness-function tooling.

**Modular monolith** — namespace domain-first (`com.app.customer.profile`), technical concern second. Monolithic structure (one repo) vs modular structure (JAR/DLL per module). Peer-to-peer communication → compile-time coupling → shared interface artifact → JAR/DLL hell; mediator decouples modules but couples everything to the mediator. Governance: every namespace under a defined module, **max ~5 total coupling points per module**, explicit forbidden dependencies.

**Pipeline** — filters are stateless single-task: **producer, transformer, tester, consumer**. Pipes unidirectional, small payloads, usually point-to-point. Bidirectional communication = wrong style or wrong filter demarcation.

**Microkernel** — core = minimum to run, or the happy path; push cyclomatic complexity into plug-ins. Only style that can be *both* domain- and technically partitioned. Core may itself be layered/modular/services. Keep the DB behind the core; plug-ins may own private stores. Ideal for per-client/per-jurisdiction variation (tax forms off a 1040 core, insurance claims rules).

**Service-based** — separately deployed UI + separately deployed coarse-grained domain services + usually one shared monolithic DB. API facade does class-level (internal) orchestration, unlike microservices' external orchestration. ACID works inside a service. Best **stepping stone** to microservices; "not every portion of an application needs microservices."

**Event-driven** — initiating event → broker (federated, domain-clustered) → event processor → **derived event**. Event = something happened, broadcast, no reply (topic/stream). Message = command/query, point-to-point, expects reply (queue). Broadcasting a command does not make it an event.
- **Poison event**: derived events loop (`inventory updated` → `stock replenished` → `inventory updated`).
- **Extensible derived event**: publish actions with no current listeners = extensibility at zero change cost.
- Async responsiveness: sync REST 50+3000+50 = 3,100 ms; async perceived 25 ms (work is still 3,025 ms). You buy responsiveness with certainty.
- **Payload**: data-based (fast, no DB read, works under restricted access — but stamp coupling, consistency, versioning, bandwidth) vs key-based (consistency, small contract, low bandwidth — but needs a read-back).
- Delivery guarantees: persistent queues + **synchronous send** + **client acknowledge** + **last participant support**; durable subscribers on JMS topics.
- Request-reply: **correlation ID** in header (preferred) vs temporary queue per request (broker bottleneck).
- **Mediator** topology (orchestration, point-to-point queues, owns workflow state and error handling) vs **choreography** (performance, scalability, no workflow owner). Multiple mediators per domain to avoid a single point of failure. Simple mediators front-end hard/complex events (BPEL/BPM behind).
- Data topologies: monolithic / domain / database-per-service — trading FT+scale+change-control against sync coupling.
- Risks: nondeterministic side effects, static coupling via contracts, too much sync, no way to know when the initiating event is "complete."

**Space-based** — remove the database from the synchronous path: replicated in-memory data grids across dynamically started **processing units**; async **data pumps** to the DB (eventual consistency). Virtualized middleware = messaging grid + data grid + deployment manager (+ optional processing grid). Every processing unit must hold *exactly* the same data.
- **Replicated** cache: fastest, no SPOF, breaks above **~100 MB** or when update rate exceeds replication capacity. **Distributed** cache: consistency, >500 MB, dynamic data, but latency + SPOF. Choose per cache, not per application.
- Near-cache (front + backing) **not recommended**: per-PU front caches desynchronize.
- Data readers only on: all cache instances crashed, all redeployed, or archived data needed.
- **Collision rate** = N × UR² / (S × RL) (instances, update rate, cache size in rows, replication latency; 100 ms is the planning default). Collisions are inversely proportional to cache size.
- Fit: **>10,000 concurrent users**, unpredictable spikes (ticketing, auctions).

**Orchestration-driven SOA** — service taxonomy: business services (coarse, no code), enterprise services (fine-grained shared atomic entities), application services (one-off), infrastructure services (monitoring, logging, auth); orchestration engine + ESB at the heart. Died because reuse became coupling: canonical `Customer` ripples everywhere; consumers carry fields they don't care about; declarative transactions (EntityBeans) failed because transactional scope can't be abstracted away at runtime; Conway's Law turned the integration team into a bottleneck. Remaining valid use: **integration architecture** only (ESB as hub, orchestration boundaries = transaction boundaries). Watch for **Accidental SOA** — the ESB gradually swallows the architecture; guard with fitness functions.

**Microservices** — "microservice is a label, not a description." Granularity heuristics: purpose (one significant behavior), transactions (things that must cooperate in a transaction belong together — distributed transactions signal too-fine granularity), choreography (isolated but chatty services should be bundled back).
- Data: no shared schemas/DBs as integration points; one domain owns the source of truth, others replicate/cache. Sharing within a broader bounded context is OK up to **~5–6 services per schema**.
- **API gateway: routing + cross-cutting concerns only. No business logic, no orchestration.**
- Cross-cutting operational concerns (circuit breakers, logging, monitoring) *should* be coupled — via **sidecar** → service mesh (Istio).
- Frontends: monolithic vs micro-frontends.
- Transactions: **don't**. If characteristics force a split, use a **saga** (mediator records success/failure; compensating transactions on failure).
- **Duplication is preferable to coupling**; duplicate code across bounded contexts rather than share a library. Shared custom libraries = coupling debt; version them if you must.
- Governance: minimize static coupling (libraries, contracts — contracts couple even over async transports); track dynamic coupling via logs/fitness functions or a registry (ZooKeeper) of interservice calls.
- Serverless is a *deployment model*, not a style.
- Antipatterns: Grains of Sand (too fine), Big Ball of Distributed Mud, distributed monolith, shared libraries, shared data.

## 8. Choosing a style (Ch. 19)

It "depends" — but on what? Prerequisites: understand the domain; run the **characteristics analysis**; know your data architecture; know the cloud deployment model (egress cost, data volume); know organizational factors (vendor cost, M&A plans, licensing); know process/team/ops maturity (microservices assume automated provisioning, testing, deployment).

**Architecture isomorphism**: does the domain's shape match the topology? Microkernel ↔ high customizability. Space-based ↔ many discrete, spiky operations. Highly coupled multi-page-form domains ↔ *not* microservices. Service-based suits intentional coupling.

Three determinations:
1. **Monolith or distributed?** One characteristic set suffices → monolith. Differing sets → distributed, and use quanta to draw the lines.
2. **Where does data live?** Monolithic / domain / per-service; data is often the real constraint.
3. **What communication style?** Default synchronous; asynchronous where rates or characteristics diverge (Payment at 1 txn/500 ms → queues as buffers). Communication style itself can redraw quantum boundaries.

Then: **least worst** set of trade-offs, recorded as ADRs + fitness functions. Style fashion moves with ecosystem change (licensing cost, open source, DevOps, cloud, containerization) — not with correctness.

## 9. Architectural patterns (Ch. 20)

Patterns are contextualized solutions; styles are named topologies. Distinguish a pattern from its implementation, and from "best practice" (best practice = permission to stop thinking).

- **Hexagonal / ports & adapters**: domain in the center, adapters around. Flaw: treats the DB as just another adapter, violating data fidelity; DDD corrected this (schemas must reflect business logic). Useful shorthand for "separate domain from operational concerns" — modern implementation is the service mesh.
- **Sidecar / service mesh**: orthogonal reuse — isolate a cross-cutting concern in a consistent layer. Costs: one sidecar per platform, sidecar bloat, implementation drift across teams.
- **Orchestration**: + centralized workflow, error handling, recoverability, queryable state. − responsiveness bottleneck, single point of failure for the workflow, weaker parallelism, tighter coupling.
- **Choreography**: + responsiveness, scalability, FT, decoupling. − no workflow owner, no centralized state, harder recoverability.
- **CQRS**: isolate writes; async-sync to read store; lets read/write differ in characteristics, model, security.
- **Single-broker** (centralized discovery, least infrastructure, FT/throughput limits) vs **domain-broker** (isolation, matches domains, scalable, more infrastructure and moving parts). Neither is a best practice.

Reuse rule: microservices decouple → duplicate domain entities, pass name-value JSON. Couple only operational capabilities.

## 10. Decisions (Ch. 21)

Antipattern progression: **Covering Your Assets** (defer from fear) → **Groundhog Day** (nobody remembers why, endless re-litigation) → **Email-Driven Architecture** (decisions lost).

- Decide at the **last responsible moment**: when the cost of deferring exceeds the risk of deciding.
- Justify technically *and* in business terms: **cost, time to market, user satisfaction, strategic positioning**. No business value → reconsider the decision.
- **Architecturally significant** (Nygard): affects structure, non-functional characteristics, dependencies, interfaces, or construction technique. A technology choice that drives a characteristic is architectural.
- **ADR**: 1–2 pages, Markdown/AsciiDoc. Title, Status, Context, Decision, Consequences (+ Compliance, Notes; optional Alternatives). Statuses: **Proposed, Accepted, Superseded** (a Proposed ADR is revised, never superseded). Cross-reference numbers.
- Decision written affirmatively ("We will use…"), never "I think…". Record the **why** (gRPC for latency) — otherwise a successor swaps in REST and gets timeouts.
- **Compliance** is part of the record: manual, or automated fitness function.
- Approval thresholds based on cost (hours × FTE rate), cross-team impact, security — write the limits down.
- Store one ADR per file in a dedicated repo/wiki, structured `application/common`, `application/<app>`, `integration`, `enterprise`. ADRs double as documentation, as standards (an unjustifiable standard shouldn't exist), and can be retrofitted onto legacy systems to expose bad design.
- Gen AI: useful for enumerating trade-offs you missed; lacks **wisdom**. Probability and "best practice" have no place in the decision.

## 11. Risk (Ch. 22)

**Risk matrix**: impact × likelihood, each 1–9; **rate impact first**, then likelihood; unknown likelihood = 3; **unknown/unproven technology = automatic 9**. Direction markers ▲ worsening, ▼ improving, ● static — with a key. Derived from continuous fitness-function measurements.

**Risk storming** (collaborative, 3 phases): **identification** (each participant alone, sticky notes colored by level) → **consensus** (post on a large diagram, argue the discrepancies) → **mitigation** (business stakeholders must be present — mitigation costs money). One criterion/context per session. Also usable on user stories (impact of not completing × likelihood of not completing).

Availability arithmetic to win arguments: 99.99% = 52.60 min/yr down; 99.90% = 8.77 h/yr. **Divide and conquer** the requirement — does the *whole* system need five nines?

## 12. Diagrams (Ch. 23)

- **Representational consistency**: always show how a view relates to the whole before switching views.
- **Irrational artifact attachment**: attachment grows with the time spent producing the artifact → keep artifacts ephemeral early (whiteboard photo, tablet + projector: infinite canvas, "what if" copy/paste, already digital).
- Tool features that matter: **layers** (base = topology, then implementation detail, then DDD boundaries/transaction scope), stencils/templates, magnets.
- Standards: UML survived mostly as class + sequence diagrams. Never rely on color alone (colorblind readers) — add iconography. Always include a key; a misinterpreted diagram is worse than none.

## 13. Teams (Ch. 24)

An architect's output only exists through the team; the architect/developer split must be **bidirectional, one virtual team**.

**Constraints define a room**: too tight → frustration, attrition; too loose → everyone becomes an architect, endless POCs. Personalities: **control freak** (too tight, writes pseudocode, steals programming), **armchair architect** (never codes, too high-level, absent), **effective** (right boundaries, removes roadblocks).

**Elastic leadership** — five factors, each ±20 on a −60…+60 scale: team familiarity (new → more control), team size (>12 → more; ≤5 → less), overall experience (junior-heavy → more), project complexity (high → more), project duration (short → *less*; long → more). Re-score continuously.

Signals a team is too large: **process loss / Brooks's Law** (look at merge conflicts), **pluralistic ignorance** (everyone privately rejects the norm and assumes they're missing something — invite the skeptic), **diffusion of responsibility** (dropped items, unclear ownership).

**Checklists**: use where steps are order-free and get skipped; never for procedural, dependent flows or familiar error-free routines. Keep short, automate anything automatable, state the obvious. Guard against the **Hawthorne effect** by announcing spot checks. The three best: code completion, unit/functional testing, software release.

**Guidance by category** (layered authority): special-purpose libraries → developer decides; general-purpose → developer analyzes, architect approves; frameworks → architect decides.

## 14. Negotiation and leadership (Ch. 25)

Negotiate from information, not authority:
- Read the jargon; quantify the nines in hours per year; divide the requirement; state **cost and time last**.
- **Demonstration defeats discussion** — benchmark instead of arguing.
- Never personal, stay calm; **justify before demanding** ("since change control matters most, that means…" not "you must").
- Let the dissenter arrive at the answer themselves — you either get buy-in or learn something.
- **4 Cs**: communication, collaboration, clear, concise.
- **Essential complexity** ("the problem is hard") vs **accidental complexity** ("we made the problem hard"). Adding accidental complexity to look valuable destroys credibility.
- Pragmatic yet visionary: account for budget, time, team skill, technical limits. Fix elasticity by finding the bottleneck first.
- Lead by example, not title. "Have you considered…" beats "what you need to do is…". Turn requests into favors; use the person's name.
- Meetings: **imposed on you** (ask why you're needed, get the agenda, attend only the relevant item, deflect to the tech lead) vs **you impose** (few, agenda'd, early morning/after lunch/end of day, protect flow state). Sit with the team.

## 15. Intersections (Ch. 26)

Architecture must stay aligned with nine facets: implementation, infrastructure, data topology, engineering practices, team topology, systems integration, enterprise, business environment, generative AI.
- Implementation: structural integrity (source tree = logical architecture; govern with ArchUnit et al.), alignment with operational concerns, constraints.
- Engineering practices: microservices assume automated provisioning/testing/deployment; measure alignment with fitness functions (agility = maintainability + testability + deployability).
- Systems integration: preserve each quantum; protocols, contracts, compatible characteristics.
- Enterprise: ignore standards and a technically fine solution gets scrapped as a "one-off."
- Business environment: **domain-to-architecture isomorphism**; cost-cutting ≠ microservices; M&A-driven growth ≠ monolith. Unknown unknowns defeat Big Design Up Front — every architecture becomes iterative. **Residuality theory**: treat change as stressors, architecture as residue.
- Gen AI: wrap LLMs behind an abstraction + modularity so engines are swappable; add **rails** and **evals**; observability. Good at enumerating risks/antipatterns/trade-offs; unreliable at "which style?" — knowledge without wisdom.

## 16. Decision rules (condensed)

- Decompose composite characteristics into measurable parts before designing; publish one definition per characteristic.
- Pick the **fewest** characteristics; force the list to 7, then to 3.
- Scope characteristics per **quantum**, not per system. One set suffices → monolith.
- Keep coupling tight inside a quantum, loose across quanta; loosen further as scope broadens.
- Measure the tail (max/p95), not the mean.
- Automate every characteristic you care about as a fitness function; explain it to developers; treat it as a checklist.
- Name components by role/action, never `Manager`/`Processor` (Entity Trap). Split when the role statement needs *and*/*also*, or when two parts need different characteristics.
- Prefer domain partitioning; iterate, don't perfect the first pass.
- Assume all 11 fallacies; quantify latency, bandwidth, transport cost, administrators before going distributed.
- Use the 80/20 rule to detect an Architecture Sinkhole.
- Never put business logic or orchestration in an API gateway.
- Never span transactions across microservices — fix granularity first; saga only when characteristics force the split.
- Never share a schema across more than ~5–6 services; never share custom libraries across bounded contexts.
- Duplicate code across bounded contexts rather than couple them.
- ESB as integration hub only; add fitness functions to stop Accidental SOA.
- Default synchronous; go async where rates, reliability, or characteristics demand it.
- Choose orchestration when workflow state/error handling/recoverability dominate; choreography when throughput, parallelism, decoupling dominate.
- Decide at the last responsible moment; justify in business terms; write the ADR; name the compliance mechanism.
- Rate impact before likelihood; unknown technology = 9; re-run risk storming at the end of every iteration.
- Show the whole before the view; keep diagrams ephemeral until the design settles; never rely on color alone.
- Re-run the trade-off analysis for every new context, and **weight** the criteria instead of tallying them.

## 17. Antipattern catalogue

Architecture by Implication · Architecture Sinkhole (80/20) · Frozen Caveman · Bottleneck Trap · Ivory Tower · Entity Trap · Generic Architecture (the Vasa) · Big Ball of Mud / unstructured monolith · Distributed Monolith / Big Ball of Distributed Mud · Grains of Sand · Covering Your Assets · Groundhog Day · Email-Driven Architecture · Accidental SOA · Irrational Artifact Attachment · Control-freak / armchair architect · Stamp coupling · Poison event · Swarm of Gnats · Out of Context (trade-off analysis without weighting).
