# architecture-skills

Agent skills for software architecture decisions.

## Skills

| Skill | Use it for |
|---|---|
| `software-architecture` | Choosing or reviewing an architecture style, drawing service/module boundaries, defining "-ilities", writing ADRs, running risk assessment, catching architecture smells |

`software-architecture` carries a 4.5k-word digest of *Fundamentals of Software Architecture* (2nd ed., Richards & Ford) in `reference/digest.md` — the entry file stays short, the digest loads on demand.

## Install

As an omp marketplace:

```
/plugin marketplace add maulvi/architecture-skills
```

Then install the `architecture-skills` plugin.

Manually:

```sh
git clone https://github.com/maulvi/architecture-skills
cp -r architecture-skills/skills/software-architecture ~/.agents/skills/
```

## What the skill enforces

- Characteristics before style: derive "-ilities" from domain concerns, cap at 7, force stakeholders to pick 3.
- Quanta before distribution: one characteristic set → monolith; differing sets → distributed, and the sets draw the boundaries.
- A style recommendation without the weighted trade-off matrix is not a recommendation.
- Hard rules that don't move: no cross-service transactions, no shared schemas past ~5–6 services, no business logic in the API gateway, no `Manager`/`Processor` components, measure the tail not the mean.
- Decisions recorded as ADRs with a compliance mechanism; what matters mechanized as fitness functions.

## Attribution

Content is distilled from *Fundamentals of Software Architecture*, 2nd edition, by Mark Richards and Neal Ford (O'Reilly). The digest is a study aid, not a substitute for the book — buy it. Terminology (architecture quantum, fitness function, connascence, the three laws) originates with the authors and the sources they cite (Page-Jones, Martin, Evans, Nygard, Constantine, Deutsch).

Code and skill structure: MIT. The distilled prose remains derivative of the source work.
