# PROJECT_CLASSIFICATION.md

## 1. Purpose

This document defines how the development control agent classifies work before choosing a governance path.

Every task must be classified across:
1. project level
2. project type
3. change risk

The governance template is derived from that combination.

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
- low impact
- manual verification is often acceptable

Governance posture:
- keep it light
- do not over-document
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
- has state/config/dependencies
- needs stability, traceability, and recovery ability

Governance posture:
- this is the default level
- standard but lightweight governance
- enough design, review, testing, release notes, monitoring, and closed loop

---

## L3 — Multi-user / higher-loss project
Typical examples:
- external-facing service
- shared system
- permission-heavy system
- deletion-capable system
- high-cost runtime outage environment

Characteristics:
- higher blast radius
- failure matters more
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

## 5. Governance template selection

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
- release/rollback notes
- baseline monitoring
- issue-to-improvement loop

---

## T3 — Reinforced mode
Use when:
- L2 + R3
- L3 + medium/high risk
- blast radius or recovery cost is meaningfully higher

Required minimum:
- stronger design and critique
- stronger validation
- staged release thinking
- clearer monitoring / stop conditions
- explicit RCA and follow-up tracking

---

## 6. Automatic risk escalation triggers

The control agent must automatically raise work to R3 if the change touches any of the following:
- memory or long-term state
- state machine or state transition rules
- task orchestration or scheduler logic
- persistence layout or migration
- deletion paths
- permission / security logic
- core external connectors
- main agent loop or control loop
- production incident fix in a long-running system

---

## 7. Default assumptions

If no better information is available:
- assume project level = `L2`
- infer project type from task content
- assume risk = `R2`
- choose template = `T2`

This default is appropriate for most persistent personal AI projects.

---

## 8. Required output format

Before starting a task, the control agent should produce a compact block like:

```md
## Task Classification
- Project Level: L2
- Project Type: C
- Change Risk: R3
- Governance Template: T3

## Why
- project is persistent
- task touches state/orchestration
- issue may not be obvious manually
- rollback and runtime observation matter
```

This block is the entry point for all downstream work.
