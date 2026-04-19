# AGENTSDLC_INSIGHTS_ADOPTED.md

## 1. Purpose

This document records the main control upgrades absorbed into Suite v2 from a stronger AgentSDLC-style governance approach.

It exists so future maintainers know **which sharpened rules were intentionally adopted**, and which parts were deliberately not copied in full.

---

## 2. Adopted into v2

### 2.1 Change class layer
Added `Class A/B/C/D` on top of `L/R/T` so that:
- runtime behavior changes
- environment/data/schema/memory changes
- self-evolution / boundary / review-bypass rule changes

can raise governance independently of project size.

### 2.2 Controller default mode
The controller is the coordinator by default, not the default primary coder.
Direct controller coding is an exception and must be marked.

### 2.3 No self-acceptance
No agent should be the sole final acceptor of its own work.
This now appears as a non-negotiable rule across the suite.

### 2.4 Stop and report instead of guessing
If blocked by missing data, API, contract, schema, or context:
- stop
- report
- avoid invented behavior

### 2.5 Structured monitoring-to-task conversion
Runtime findings are now expected to carry:
- source
- source_type
- source_id
- signal_level
- evidence
- affected_surface
- auto_processible
- priority
- category

### 2.6 Duplicate checking before new issue creation
The suite now recommends checking for existing open tasks with the same source identity before opening new issue records.

### 2.7 Three runtime handling paths
The suite now formalizes:
- Path A — bounded auto-processible issue
- Path B — controller-triaged issue
- Path C — incident / hotfix

### 2.8 Hotfix must return to the main lifecycle
Emergency stabilization is allowed, but permanent fix + preventive measures must re-enter the standard lifecycle.

### 2.9 Health-based issue closure
Issues should not be considered closed merely because code was deployed.
The preferred rule is healthy signal behavior across 3 consecutive observation cycles.

### 2.10 Pipeline depth limit
The suite now explicitly discourages:
- more than 3 simultaneous active implementation/review/validation tasks
- more than 1 simultaneous promotion

---

## 3. Intentionally not copied in full

The following ideas were **not** adopted verbatim because this suite is optimized for smaller personal projects:

- mandatory full 7-gate chain for every meaningful change
- fully separated agent org structure for every stage
- enterprise-like environment rigidity for every project
- aggressive always-on monitoring expectations for low-persistence tools
- absolute prohibition on controller coding

Instead, the suite keeps:
- lightweight defaults
- dynamic escalation
- stronger rules only when risk or class justifies them

---

## 4. Decision rule

When future maintainers are unsure whether to upgrade the suite further:

- preserve lightweight defaults for L1/L2 work
- preserve the sharper non-negotiable rules
- only import heavier SDLC mechanics when repeated incidents or scaling pressure justify them
