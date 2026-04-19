# PROJECT_CLASSIFICATION.md

## 1. Purpose

This document defines how the development control agent classifies work before choosing a governance path.

Every task must be classified across **four** dimensions:
1. project level
2. project type
3. change risk
4. change class

The governance template is derived from the combination, but **change class can raise the minimum required governance even if the base project is small**.

---

## 2. Project level

## L1 — One-off / low-persistence project
Typical examples:
- temporary scripts
- demo-only prototypes
- one-time data processing
- local experiments
- short-life utilities

Characteristics:
- short-lived
- few users
- low blast radius
- manual verification is often acceptable

Governance posture:
- keep it light
- avoid over-documentation
- still require explicit validation and rollback awareness

---

## L2 — Persistent personal project
Typical examples:
- personal API service
- long-running AI agent
- recurring automation system
- private workflow or knowledge system
- scheduled small service

Characteristics:
- long-running
- repeatedly iterated
- has state / config / dependencies
- needs stability, traceability, and recovery ability

Governance posture:
- this is the default level
- standard but lightweight governance
- enough design, review, validation, release notes, monitoring, and closed loop

---

## L3 — Multi-user / higher-loss project
Typical examples:
- external-facing service
- shared system
- permission-heavy system
- deletion-capable system
- high-cost outage environment

Characteristics:
- higher blast radius
- failures matter more
- stronger control needed

Governance posture:
- reinforced review, validation, release, monitoring, and incident handling

---

## 3. Project type

## Type A — Script / automation task
Common examples:
- batch script
- one-shot sync
- file transformation tool
- cron-based processing

Main concerns:
- input/output correctness
- idempotency
- retry safety
- clear logs

---

## Type B — App / API / website
Common examples:
- web app
- REST API
- dashboard
- mini backend

Main concerns:
- main-path usability
- compatibility
- error handling
- reversible deployment

---

## Type C — AI agent / workflow system
Common examples:
- tool-using agent
- multi-step workflow
- planning / memory / orchestration system
- RAG / reasoning workflow

Main concerns:
- context correctness
- state transition closure
- tool invocation boundaries
- failure exit / fallback
- fake completion prevention
- task drift prevention

---

## Type D — Integration / data flow / connector
Common examples:
- webhook pipeline
- external SaaS connector
- data synchronization
- queue-based integration
- API bridge

Main concerns:
- protocol stability
- timeout / retry / limit behavior
- duplicate event handling
- consistency

---

## 4. Change risk

## R1 — Low risk
Typical cases:
- docs or comments
- tiny UI tweaks
- very local non-core fixes
- non-behavioral cleanup

Expected governance:
- T1 or minimal T2 depending on project level

---

## R2 — Medium risk
Typical cases:
- standard feature addition
- local core logic modification
- ordinary refactor
- prompt / workflow tuning
- normal API change

Expected governance:
- usually T2 for persistent projects

---

## R3 — High risk
Typical cases:
- state logic
- memory logic
- scheduler or orchestration changes
- persistence or schema changes
- permissions / security / deletion paths
- external core integration changes
- production incident fixes
- core agent loop behavior changes

Expected governance:
- T3 unless the project is truly trivial and isolated

---

## 5. Change class

Change class is different from risk:
- **risk** describes how dangerous this specific modification is
- **class** describes what kind of system surface is being changed and therefore what minimum validation chain must apply

## Class A — Safe local change
Typical cases:
- small bug fix
- narrow route extension
- focused test addition
- docs correction
- isolated non-runtime cleanup

Minimum expectations:
- lightweight brief allowed
- focused validation
- controller review still required if the change affects runtime

Typical fit:
- L1/L2 work with narrow surface and easy rollback

---

## Class B — Runtime behavior change
Typical cases:
- new runtime route
- API behavior change
- workflow branch change
- state machine behavior change
- prompt/controller decision boundary change

Minimum expectations:
- full delivery chain for the selected template
- focused tests plus integration/smoke checks as needed
- release note and monitoring awareness

Typical fit:
- default class for many persistent personal projects

---

## Class C — Environment / data / schema / memory change
Typical cases:
- database migration
- persistent memory contract change
- production config change
- knowledge base wiring change
- scheduler persistence change
- release process change with stateful consequences

Minimum expectations:
- explicit rollback note
- backup/snapshot readiness where applicable
- migration or compatibility validation
- enhanced release caution

Typical fit:
- usually raises work to `T3` for L2/L3 projects

---

## Class D — Self-evolution / boundary / auto-modification rule change
Typical cases:
- changing what the system may rewrite automatically
- changing what agents may mutate directly
- changing review bypass rules
- changing controller authority boundaries
- changing memory self-edit permissions
- changing how auto-fixes are allowed to land

Minimum expectations:
- highest scrutiny
- explicit controller review
- explicit risk note
- explicit rollback plan
- no direct production landing
- rollback drill or equivalent confidence-building validation for meaningful changes

Typical fit:
- always treated as high-governance work

---

## 6. Base governance template selection

## T1 — Lightweight mode
Use when:
- L1 + R1
- L1 + small R2
- work is short-lived and easily reversible

Required minimum:
- brief plan
- self-review
- main-path validation
- simple logging
- rollback note

---

## T2 — Standard mode
Use when:
- L2 + R1/R2
- L1 + R3
- persistent project work that is important but not deeply critical

Required minimum:
- standard design note
- independent review perspective
- core validation
- release / rollback notes
- baseline monitoring
- issue-to-improvement loop

---

## T3 — Reinforced mode
Use when:
- L2 + R3
- L3 + medium/high-risk work
- anything requiring strengthened release discipline

Required minimum:
- stronger design and review
- stronger validation and release discipline
- explicit incident / rollback readiness
- closed-loop follow-through

---

## 7. Change-class override rules

After selecting a base template, apply these overrides:

### Class A override
- keep the base template unless other risk signals force a higher path

### Class B override
- minimum template is `T2` for persistent projects
- `T1` is only acceptable if the project is truly one-off and rollback is trivial

### Class C override
- minimum template is `T2`
- for `L2` or `L3`, default to `T3`
- require explicit rollback and compatibility considerations

### Class D override
- always force `T3`
- always force `R3`
- no self-acceptance
- no direct production landing
- no silent delegation of approval authority

---

## 8. Practical selection algorithm

The control agent should choose in this order:

1. determine `Project Level`
2. determine `Project Type`
3. determine `Change Risk`
4. determine `Change Class`
5. select base template from `Level + Risk`
6. raise template if `Change Class` requires it
7. note special controls from `Project Type`

---

## 9. Default assumptions

If the task is unclear, use:
- project level = `L2`
- project type = most likely `B`, `C`, or `D`
- change risk = `R2`
- change class = `B`
- template = `T2`

Then upgrade when evidence shows:
- state, memory, scheduler, persistence, deletion/permission, external connector behavior, production incident repair, or core agent loop -> `R3`
- environment/data/schema/memory change -> at least `Class C`
- boundary/authority/review-bypass/auto-modification rule change -> `Class D`

---

## 10. Required task classification output

Every task must begin with this structure:

```md
## Task Classification
- Project Level: L1 / L2 / L3
- Project Type: A / B / C / D
- Change Risk: R1 / R2 / R3
- Change Class: A / B / C / D
- Governance Template: T1 / T2 / T3

## Why
- concise reasons
```

This output is mandatory. No substantial work should proceed before classification.
