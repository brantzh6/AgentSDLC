# REVIEW_CHECKLISTS.md

## 1. Purpose

This document contains the practical checklists the control agent should use during design, implementation, validation, release, monitoring, and closed-loop work.

Use these checklists to avoid missing key quality controls without requiring heavy process.

---

## 2. Design checklist

The control agent must ask:

### Basic
- Is the goal explicit?
- Are non-goals explicit?
- Is the task understood correctly?
- Is this over-designed for the project size?

### Risk
- What is most likely to go wrong?
- What could break existing behavior?
- Is there a hidden state or dependency risk?
- Is there a blast-radius concern?

### Validation
- How will success be proven?
- What will be manually checked?
- What must be tested?
- What remains unverified?

### Release / rollback
- How will this be released?
- What is the simplest rollback?
- What data or state might be hard to recover?

---

## 3. Plan review checklist

### T1
- Did we understand the task correctly?
- Is there a simpler route?
- Are there obvious hidden assumptions?
- Are we building too much for a small task?

### T2
- Is the plan aligned with the real goal?
- Is complexity appropriate?
- Are failure paths included?
- Is it testable?
- Is it monitorable?
- Is the release / rollback realistic?

### T3
In addition to T2:
- Are state transitions closed?
- Are consistency risks addressed?
- Are dependency failures handled?
- Does the plan add uncontrolled operational burden?
- Is there a clear runtime observation approach?

---

## 4. Code review checklist

### Minimal set
- Is the change aligned with the approved plan?
- Is there an obvious bug?
- Did scope silently expand?
- Are key assumptions hidden in code?
- Are errors swallowed silently?
- Is rollback still possible?

### Standard set
- Does it affect existing functionality?
- Is compatibility preserved where needed?
- Are logs sufficient for debugging?
- Are failure paths explicit?
- Does the validation cover the main risk?

### Reinforced set
- Is the state machine closed?
- Are retries / timeouts / fallbacks safe?
- Are consistency assumptions explicit?
- Are external dependency failures handled?
- Are monitoring hooks sufficient?
- Is rollback genuinely executable?

---

## 5. Validation checklist

### Core
- Was the main path verified?
- Was at least one failure path verified?
- Were high-risk parts tested or explicitly acknowledged?
- Was release-critical behavior manually checked when needed?
- Are remaining risks stated clearly?

### For Type A
- Can it run twice safely?
- What happens with bad input?
- What happens after interruption?

### For Type B
- Do core interfaces still work?
- Does the main user flow still work?
- Are errors understandable?

### For Type C
- Does state progress correctly?
- Do tool calls behave as expected?
- What happens with missing context?
- What happens on tool failure?
- Can the system exit / degrade cleanly?

### For Type D
- What happens on timeout?
- What happens on repeated callbacks?
- What happens on duplicate input?
- Are retries safe?

---

## 6. Release checklist

Before release, confirm:
- Do we have a short change summary?
- Do we know how to roll back?
- Is there a fallback / kill option?
- Do we know what to watch after release?
- Is the previous version still available?
- Do we know when to stop or reverse?

For T3:
- Is staged release possible?
- Are stop conditions explicit?
- Is runtime observation prepared before rollout?

---

## 7. Monitoring checklist

### Baseline
- Can we tell whether the service/job is alive?
- Can we tell whether errors are increasing?
- Can we tell whether the main task is succeeding?
- Can we tell whether external dependencies are failing?

### Better coverage
- Can we correlate issues to a version or release?
- Can we identify the failing stage quickly?
- Do logs include enough context?
- Do we have too much noise to act effectively?

---

## 8. Closed-loop checklist

After a runtime problem:
- Did we only patch the visible symptom?
- Did we identify the real trigger?
- Did we improve at least one of:
  - design
  - test
  - review checklist
  - monitoring
  - rule set
  - runbook
- Is the chance of recurrence lower now?
- Did we record the lesson in a reusable form?

---

## 9. Language control checklist

The control agent must reject vague completion language.

Unacceptable:
- should work
- probably fixed
- likely safe
- theoretically complete
- should pass

Preferred:
- implemented
- validated
- not validated
- needs manual confirmation
- remaining risk exists
