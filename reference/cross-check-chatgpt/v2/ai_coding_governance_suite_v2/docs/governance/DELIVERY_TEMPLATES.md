# DELIVERY_TEMPLATES.md

## 1. Purpose

This document describes what "good enough" delivery looks like for each governance template.

The goal is consistency, not document bloat.

Every delivery should also carry its `Change Class`.

---

## 2. T1 — Lightweight mode

Use for small, bounded, low-persistence work.

Required output:
- classification (including change class)
- brief plan
- self-review note
- implementation summary
- validation note
- rollback note

Minimum validation:
- main path exercised
- 2–5 important scenarios checked
- 1 failure path checked

Promotion posture:
- simple and reversible
- usually direct local deployment or personal runtime update
- still requires post-change sanity check

---

## 3. T2 — Standard mode

Use for most persistent personal project work.

Required output:
- classification (including change class)
- design note
- independent review note
- implementation summary
- validation result
- release note
- rollback note
- monitoring observation points

Minimum validation:
- core logic validated
- main-path smoke test
- at least 1 failure path
- at least 1 regression check
- critical flow manually checked before promotion

Promotion posture:
- observable and reversible
- stop-loss method defined
- post-release observation required

---

## 4. T3 — Reinforced mode

Use for higher-risk, Class C/D, or production-sensitive work.

Required output:
- classification (including change class)
- stronger design note with risk handling
- stronger review result
- implementation summary
- stronger validation result
- release checklist
- rollback plan
- monitoring / signal mapping
- residual risk note

Minimum validation:
- T2 minimums
- stronger boundary checks
- stronger failure-path checks
- rollback feasibility check
- if meaningful, simple load/perf sanity check
- for Class D, explicit scrutiny of authority/boundary implications

Promotion posture:
- cautious
- ideally staged or otherwise carefully observed
- do not overload the promotion pipeline
- direct production landing is prohibited for Class D

---

## 5. Controller-coded exception

If the controller directly edits production code, add an explicit exception note:
- why delegation was not used
- why the change was bounded
- how no self-acceptance was preserved
- how review/validation still happened

This note may be short, but it must exist.
