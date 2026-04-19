# AI_PROJECT_GUARDRAILS.md

## 1. Purpose

This document defines extra controls for AI agent / workflow / memory / orchestration systems.

These controls always apply in addition to the base governance suite.

The goal is to prevent the most common AI coding failures:
- fake completion
- context drift
- hidden scope expansion
- over-trusting model inference
- silent damage to memory/state/authority boundaries
- repeated runtime improvisation without rule hardening

---

## 2. Core AI-specific rules

### 2.1 No fake completion
The following statements are not acceptable as completion claims:
- should work
- probably fixed
- logically complete
- likely releasable
- should pass tests

Use instead:
- implemented
- validated
- not validated
- requires manual confirmation
- risk remains

### 2.2 No self-acceptance
A model cannot be the only judge of its own output.
Even when one model plays multiple roles, review must be explicit and role-separated.

### 2.3 Stop and report instead of guessing
If blocked by:
- missing schema
- missing API contract
- unclear memory semantics
- unclear runtime state meaning
- insufficient external context

then:
- stop
- state what is unknown
- propose the minimum next action

Do not guess the missing truth surface.

### 2.4 Distinguish deterministic logic from inference
Explicitly distinguish:
- deterministic code path
- model inference
- prompt shaping
- tool output
- memory retrieval
- controller decision logic

Do not collapse these into one blurred behavior layer.

---

## 3. Risk auto-escalation rules

The following areas are auto-upgraded to `R3`, and usually to `Class C` or `D`:

### Class C candidates
- persistent memory contract
- state schema
- scheduler persistence
- runtime configuration affecting truth or behavior
- vector / knowledge wiring
- data migration
- release process changes affecting runtime state

### Class D candidates
- changing what the system may edit automatically
- changing what agents may mutate directly
- changing review bypass rules
- changing controller or reviewer authority
- changing self-evolution permissions
- changing auto-fix landing permissions

If unsure between C and D:
- choose **D** if the change affects **authority, mutation permission, or bypass rights**
- choose **C** if it affects **state, memory, data, config, or compatibility**

---

## 4. Controller discipline for AI projects

The controller is not the default production coder.
Its normal role is to:
- classify
- constrain
- brief
- review
- validate
- decide promotion
- triage runtime issues

Controller direct coding is an exception.
When it happens, it must be marked and still cannot self-accept.

The controller must also enforce pipeline discipline:
- max 3 active tasks in implementation/review/validation
- max 1 active task in promotion
- do not open new major branches when overloaded

---

## 5. AI-specific review lenses

During review, explicitly check:

### 5.1 Context integrity
- is the required context actually present?
- does the workflow behave sensibly with missing context?
- is context freshness important, and if so, how is that handled?

### 5.2 Boundary integrity
- are tool permissions bounded?
- is write scope bounded?
- can the agent mutate surfaces it should not touch?
- did the task silently alter boundary rules?

### 5.3 State closure
- do state transitions close properly?
- what happens on retries?
- what happens on partial failure?
- what happens when memory is missing or stale?

### 5.4 Truth-surface integrity
- which surfaces are treated as truth?
- are chat outputs being mistaken for durable state?
- is memory being treated as truth without validation?
- did the change blur truth vs interpretation?

---

## 6. AI-specific validation

Minimum validation for Type C systems should include:
- expected context path
- missing context path
- tool success path
- tool failure path
- malformed input path
- exit / degradation path

If memory or orchestration is involved, add:
- stale memory path
- duplicate task / repeated execution path
- interrupted loop recovery path
- conflicting signal / ambiguous state path

If the task affects authority or self-modification rules:
- treat it as Class D
- require explicit controller sign-off
- require explicit rollback statement
- do not land directly to production

---

## 7. Monitoring guardrails for AI systems

Monitoring should not only track uptime.
It should help expose:
- false success
- loop drift
- repeated retries
- stuck tasks
- tool failure clusters
- memory pollution signals
- unexpected controller behavior changes

Runtime signals should be convertible into structured issues with:
- source
- source_type
- source_id
- signal_level
- evidence
- affected_surface
- category
- priority

---

## 8. Anti-patterns

The control agent must reject these patterns:
- "the model knows what to do" as a substitute for design
- "add more prompt text" as the only fix
- "just add logs" as the permanent fix
- "let it run and see" for Class C/D work
- letting self-evolution proposals bypass normal review
- treating a hotfix as permanent closure
- opening many parallel branches until no one knows what is trusted

---

## 9. Durable improvement requirement

For AI projects, incidents and repeated review failures should be converted into one or more of:
- checklist item
- rule card
- test case
- monitoring signal
- runbook update
- prompt / guardrail update
- boundary restriction

The project should improve by becoming harder to fool, harder to drift, and harder to mutate incorrectly.
