# Stage Checklists

Practical checklists organized by governance tier (T1/T2/T3) for each
lifecycle stage. Use these to avoid missing key quality controls without
requiring heavy process.

For full lifecycle context, see `governance/LIFECYCLE_CONTRACT.md`.

## 1. Classification Checklist

Before any substantial work begins:

- [ ] Project level chosen (L1/L2/L3)?
- [ ] Project type chosen (A/B/C/D)?
- [ ] Change risk chosen (R1/R2/R3)?
- [ ] Change class chosen (A/B/C/D)?
- [ ] Governance template chosen (T1/T2/T3)?
- [ ] Gate profile mapped (G-Lite/G-Std/G-Full)?
- [ ] If Class C or D, was the template raised appropriately?
- [ ] If memory/state/orchestration/permissions touched, was risk raised to R3?
- [ ] If Class D, is R3 and T3 forced?
- [ ] Task ID assigned?

## 2. Design Checklist

### T1 (Lightweight)

- [ ] Is the goal explicit?
- [ ] Are non-goals stated?
- [ ] What is the main risk?
- [ ] How will success be verified?
- [ ] What is the rollback path?

### T2 (Standard)

All T1 items plus:
- [ ] Is the scope boundary clear (in-scope / out-of-scope)?
- [ ] Are affected files identified?
- [ ] Are constraints and assumptions documented?
- [ ] Are success criteria measurable?
- [ ] Are stop conditions defined (when to abort)?
- [ ] Is the change class determined (A/B/C/D)?
- [ ] Is this over-designed for the task size?

### T3 (Reinforced)

All T2 items plus:
- [ ] Is the current state analyzed?
- [ ] Are at least two alternatives compared?
- [ ] Are trade-offs explicit per option?
- [ ] Is the monitoring strategy defined?
- [ ] Is the failure handling strategy defined?
- [ ] Are state transitions closed?
- [ ] Are blast-radius concerns addressed?

## 3. Design Review Checklist

### T1 (Self-Review)

- [ ] Did I understand the task correctly?
- [ ] Is there a simpler approach?
- [ ] Are there obvious hidden assumptions?
- [ ] Am I building too much for a small task?

### T2 (Role-Switching Review)

- [ ] Is the plan aligned with the real goal?
- [ ] Is complexity appropriate?
- [ ] Are failure paths included?
- [ ] Is it testable?
- [ ] Is it monitorable?
- [ ] Is the release/rollback realistic?
- [ ] Review result: approve / conditional_approve / reject

### T3 (Full Review)

All T2 items plus:
- [ ] Are state transitions closed?
- [ ] Are consistency risks addressed?
- [ ] Are dependency failures handled?
- [ ] Does the plan add uncontrolled operational burden?
- [ ] Is there a clear runtime observation approach?
- [ ] Review result documented with findings

## 4. Code Review Checklist

### T1 (Minimal Self-Check)

- [ ] Does the code match the plan?
- [ ] Any obvious bugs?
- [ ] Did scope silently expand?
- [ ] Are errors swallowed silently?

### T2 (Standard Review)

All T1 items plus:
- [ ] Does it affect existing functionality?
- [ ] Is compatibility preserved where needed?
- [ ] Are logs sufficient for debugging?
- [ ] Are failure paths explicit?
- [ ] Does the validation cover the main risk?
- [ ] Is rollback still possible?

### T3 (Reinforced Review)

All T2 items plus:
- [ ] Is the state machine closed?
- [ ] Are retries/timeouts/fallbacks safe?
- [ ] Are consistency assumptions explicit?
- [ ] Are external dependency failures handled?
- [ ] Are monitoring hooks sufficient?
- [ ] Is rollback genuinely executable?

## 5. Validation Checklist

### T1 (Focused Check)

- [ ] Was the main path exercised?
- [ ] Was at least 1 failure scenario checked?
- [ ] Are remaining risks stated?

### T2 (Standard Testing)

All T1 items plus:
- [ ] Was the existing test suite run (no regressions)?
- [ ] Were focused tests added for changed behavior?
- [ ] Was at least 1 regression path checked?
- [ ] Were high-risk parts tested or explicitly acknowledged?

### T3 (Full Testing)

All T2 items plus:
- [ ] Were integration tests run?
- [ ] Were smoke tests run against live-like environment?
- [ ] Was migration validation done (if applicable)?
- [ ] Was rollback feasibility checked (Class C+)?
- [ ] Was rollback drill done (Class D)?

### Type-Specific Validation

#### Type A (Script/Automation)
- [ ] Can it run twice safely (idempotent)?
- [ ] What happens with bad input?
- [ ] What happens after interruption?

#### Type B (App/API)
- [ ] Do core interfaces still work?
- [ ] Does the main user flow still work?
- [ ] Are errors understandable?

#### Type C (AI Agent/Workflow)
- [ ] Does state progress correctly?
- [ ] Do tool calls behave as expected?
- [ ] What happens with missing context?
- [ ] What happens on tool failure?
- [ ] Can the system exit/degrade cleanly?

#### Type D (Integration/Connector)
- [ ] What happens on timeout?
- [ ] What happens on repeated callbacks?
- [ ] What happens on duplicate input?
- [ ] Are retries safe?

## 6. Release Checklist

### T1 (Simple Deploy)

- [ ] Is the old version still available?
- [ ] Do I know the rollback path?
- [ ] Run the main path after deploy

### T2 (Staged Deploy)

All T1 items plus:
- [ ] Scope is frozen (no late additions)
- [ ] Review is complete
- [ ] Validation is complete with evidence
- [ ] Git checkpoint exists (commit)
- [ ] Rollback pointer exists
- [ ] Environment target is explicit
- [ ] What to watch after release is known
- [ ] Stop/reverse conditions are known

### T3 (Full Promotion)

All T2 items plus:
- [ ] Staging validation passed
- [ ] Post-promotion verification planned
- [ ] Observation window defined
- [ ] Stop conditions explicit before rollout
- [ ] Backup readiness confirmed
- [ ] Rollback path still valid at every step
- [ ] Promotion pipeline within WIP limit (max 1 active promotion)
- [ ] For Class C: migration/compatibility validated
- [ ] For Class D: explicit governance sign-off documented
- [ ] For Class D: no direct production landing
- [ ] For Class D: rollback drill or equivalent completed
- [ ] Environment path correct (dev->staging->prod, no shortcut)

## 7. Monitoring Checklist

### T1 (Basic Health)

- [ ] Can I tell whether it is alive?
- [ ] Can I tell whether failures are increasing?
- [ ] Can I tell whether the main task is succeeding?

### T2 (Standard Monitoring)

All T1 items plus:
- [ ] Are critical dependencies being watched?
- [ ] Can I correlate issues to a version/release?
- [ ] Can I identify the failing stage quickly?
- [ ] Do logs include enough context?
- [ ] Is there too much noise to act effectively?

### T3 (Full Monitoring)

All T2 items plus:
- [ ] Are minute-level health checks running?
- [ ] Are canary tests running?
- [ ] Is performance regression being tracked?
- [ ] Are daily reports being generated?
- [ ] Is the monitoring system itself being monitored?

## 8. Closed-Loop Checklist

After a runtime problem (all tiers):

- [ ] Was duplicate task lookup done before creating new issue?
- [ ] Did we only patch the visible symptom, or fix the real cause?
- [ ] Did we identify the real trigger?
- [ ] Did we add at least one durable improvement:
  - [ ] design correction
  - [ ] test case
  - [ ] review checklist update
  - [ ] monitoring point
  - [ ] rule/guardrail
  - [ ] runbook update
- [ ] Is the chance of recurrence lower now?
- [ ] Did we record the lesson in a reusable form?
- [ ] Did closure wait for 3 healthy observation cycles (not just deployment)?

## 9. Language Control Checklist

Before marking any task as complete:

- [ ] No vague language used ("should work", "probably fixed")
- [ ] All claims backed by evidence
- [ ] Anything not verified is marked as `unverified` or `inferred`
- [ ] Remaining risks are explicitly stated
- [ ] Status uses precise language: implemented / validated / unverified
