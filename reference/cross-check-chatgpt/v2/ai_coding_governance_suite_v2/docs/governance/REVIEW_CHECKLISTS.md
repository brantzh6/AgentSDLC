# REVIEW_CHECKLISTS.md

## 1. Purpose

This document contains practical stage-by-stage checklists for the development control agent.

Use these as compact operating lists, not as bureaucracy generators.

---

## 2. Classification checklist

Before any substantial work begins, confirm:
- project level chosen?
- project type chosen?
- change risk chosen?
- change class chosen?
- governance template chosen?
- if Class C or D, was the template raised appropriately?
- if memory/state/orchestration/permissions are touched, was risk raised?

---

## 3. Design checklist

Check:
- is the problem clearly stated?
- is the goal explicit?
- are non-goals explicit?
- is the scope bounded?
- are the main risks named?
- is validation described?
- is release / rollback considered?
- does the chosen design match the change class?
- if blocked by unknowns, is that explicitly called out instead of guessed?

---

## 4. Design review checklist

Check:
- did the plan solve the actual problem?
- is there a simpler safe approach?
- are hidden assumptions exposed?
- are failure paths considered?
- is the design testable?
- is the design monitorable?
- is rollback realistic?
- for Class D: does the change alter authority, review bypass, mutation scope, or self-evolution rights?

Decision values:
- pass
- conditional pass
- reject

Record:
- required changes
- risk points
- deferrable items

---

## 5. Implementation checklist

Check:
- implementation stayed inside the brief?
- no silent scope broadening?
- no invented API/schema/behavior?
- controller-coded exception marked if applicable?
- key logic explicit enough to review?
- errors not silently swallowed?
- external calls have failure handling?
- logs or signals added where needed?
- for Type C: deterministic logic vs model inference clearly separated?

---

## 6. Code review checklist

Check:
- matches approved plan?
- runtime behavior impact understood?
- known risk surface documented?
- backward compatibility considered?
- no silent failure pattern introduced?
- observability worsened or improved?
- rollback surface changed?
- for Class C: migration/compatibility implications reviewed?
- for Class D: governance, authority, or boundary impact explicitly reviewed?

---

## 7. Validation checklist

### Core checks
- main path validated?
- at least one failure path validated?
- residual gaps documented?
- recommendation explicit?

### Class-aware checks
#### Class A
- focused validation run?
- local runtime sanity checked?

#### Class B
- focused validation run?
- integration or smoke path checked where relevant?
- runtime-facing behavior confirmed?

#### Class C
- compatibility / migration check done?
- rollback feasibility checked?
- backup/snapshot readiness confirmed when relevant?

#### Class D
- boundary / authority impact reviewed?
- rollback plan explicit?
- no direct production landing?
- stronger scrutiny applied?

### Type-aware checks
#### Type A
- repeat execution safe?
- input anomaly handled?

#### Type B
- core API/page/feature path checked?

#### Type C
- expected context path checked?
- missing context path checked?
- tool success path checked?
- tool failure path checked?
- bad input path checked?
- exit/degradation path checked?

#### Type D
- timeout / retry path checked?
- duplicate event / callback path checked?
- consistency risk considered?

---

## 8. Release checklist

Check:
- change summary written?
- rollback path written?
- old version recoverable?
- stop-loss method defined?
- post-release observation items defined?
- if Class C/D, was release handled cautiously enough?
- if Class D, direct production landing prevented?
- promotion load within WIP limit?

---

## 9. Monitoring checklist

Check:
- can we tell if the service/job is alive?
- can we tell if errors increased?
- can we tell if the core task succeeded?
- can we tell if a dependency is failing?
- can we map a signal back to release/task context?
- are critical findings structured enough to become tasks?
- if an issue recurs, does it point to missing rule/test/monitoring control?

---

## 10. Incident / closure checklist

Check:
- was damage stopped first?
- was the issue converted into structured evidence?
- was duplicate task lookup done?
- was temporary mitigation separated from permanent fix?
- was root cause captured beyond surface symptom?
- was at least one durable improvement added?
- did the permanent fix re-enter the lifecycle if needed?
- did closure wait for healthy post-fix monitoring, not just deployment?

Recommended closure rule:
- do not mark the issue closed until the relevant signal stays healthy for **3 consecutive observation cycles** or an explicitly justified equivalent.
