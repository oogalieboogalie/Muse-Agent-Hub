# Ghost Architect Hunt Kit

Your complete bug bounty hunting system, assembled 2026-09-20. Five pieces, one workflow.

---

## The pieces

| # | Piece | What it is | When to use it |
|---|---|---|---|
| 1 | [Bug Bounty Mindset Skill](sandbox://workspace/skills/bug-bounty-mindset/SKILL.md) | Your distilled reasoning partner: the five Ghost Architect lenses (Lopez, Rosen, Curry, DeVoss, Specialist) plus inference chains, human-chain profiling, and cross-target propagation | **During** the hunt, when you share an observation and want to reason through what it means |
| 2 | [System Spec](sandbox://workspace/hunts/ghost-architect-kit/system-spec.md) | The full Ghost Architect v1.0 operating spec: core principles, the four concurrent loops, memory architecture, phased decision framework, finding response format | **Before** a hunt, to set the operating frame; reference mid-hunt for the phase checklists |
| 3 | [Primitive Library](sandbox://workspace/hunts/ghost-architect-kit/primitive-library.md) | Living bypass and mutation database (PRIM-001 octal IP SSRF, PRIM-002 double-encode traversal, PRIM-003 unicode normalization, ...) with target conditions, mutations, cross-pollination queue, confidence scoring | **During** the hunt, when you need a bypass to replay and scale across endpoints |
| 4 | [Reflection Template](sandbox://workspace/hunts/ghost-architect-kit/reflection-template.md) | Fillable markdown post-engagement debrief: one copy per target per session, filed in `reflections/` | **After** every hunt, win or lose |
| 5 | [CTBB Playbook](sandbox://workspace/hunts/ctbb/PLAYBOOK.md) | 3,800-word synthesis distilled from 140 Critical Thinking Bug Bounty podcast transcripts: 22 cross-corroborated techniques, 5 methodological disagreements with judgments, ranked top-20 tactics | **Anytime** you need technique depth behind a loop (e.g. what "boring" actually means in practice) |

Original PDF of the reflection template is kept as `reflection-template.pdf` for reference.

---

## The workflow

```
BEFORE                          DURING                          AFTER
  |                               |                               |
  v                               v                               v
System Spec                   Mindset Skill                   Reflection Template
(set the frame:                (think out loud:                (debrief: what worked,
 which loops,                   inference chains,               what didn't, what
 which phases)                   what does this                 primitives were born,
                                 weird thing mean?)             what did I miss?)
                                      |
                                      v
                              Primitive Library               --> new primitives get
                              (one bypass found?               cataloged back into
                               generate 10+ mutations,          the library with
                               replay globally,                success history
                               cross-pollinate)
                                      |
                                      v
                              CTBB Playbook
                              (technique depth:
                               the "how" behind
                               each loop)
```

---

## The four loops, in plain language

1. **Rosen Loop** — Map the infrastructure graph before touching anything. DNS chain, third parties, trust boundaries. *Understand before testing.*
2. **Lopez Loop** — Sweep the boring surface relentlessly. Exposed files, default creds, misconfig headers. *Statistical arbitrage on everyone else's blind spots.*
3. **Curry Loop** — Follow the weird. Anomalous response time, strange error, odd behavior across roles. Spawn a curiosity thread, follow it to a finding or a negative. *Curiosity finds what checklists miss.*
4. **DeVoss Loop** — Scale the primitive. One bypass becomes 10+ mutations, replayed across every matching endpoint, cross-pollinated against other vuln classes. *One insight scaled horizontally.*

Plus the **Specialist** lens: when the tech stack matches deep domain knowledge (cloud, mobile/API, legacy enterprise), go deep in that technology.

---

## Rules of the kit

- **One reflection per session.** Copy `reflection-template.md` into `reflections/` as `<target>-<date>.md`. Even (especially) for dry holes. The NBA hunt would have been a perfect first entry.
- **The primitive library is living.** Every new bypass gets a PRIM-XXX entry with mutations, target conditions, and success history. Confidence scoring decides what auto-replays.
- **Cross-link the playbook.** When a loop references a technique ("check boring surface"), the CTBB playbook has the corroborated how.
- **Park fast, return smart.** Section 7 and 10 of the reflection exist so parked targets become return targets with a plan, not vague guilt.
