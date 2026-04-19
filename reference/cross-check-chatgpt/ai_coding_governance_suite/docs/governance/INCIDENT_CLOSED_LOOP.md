# INCIDENT_CLOSED_LOOP.md

## 1. Purpose

This document defines the runtime issue handling and closed-loop improvement model for small or personal AI coding projects.

The objective is not to create heavy incident management bureaucracy. The objective is to ensure that runtime problems become durable improvements instead of repeated firefighting.

---

## 2. Core principle

A runtime issue is not complete when the service works again.

A runtime issue is complete only when:
1. service is stabilized
2. root cause is understood well enough
3. at least one durable anti-recurrence improvement is made
4. that improvement is tracked to completion

---

## 3. When to invoke this process

Use this process for:
- runtime errors
- repeated failed tasks
- agent loops going off-course
- broken external integrations
- state corruption or unexpected state drift
- false success signals
- failures discovered through logs, alerts, user complaints, or manual observation

---

## 4. Severity model

Use a lightweight severity model.

### P0
- system unavailable
- data corruption risk
- security risk
- destructive incorrect behavior

### P1
- critical function broken
- repeated high-value task failure
- severe workflow degradation

### P2
- partial or local issue
- degraded but usable service
- recurring non-fatal defect

### P3
- minor defect
- low-impact issue
- non-urgent weakness or warning sign

This severity is used to decide urgency, not to create formal bureaucracy.

---

## 5. Runtime issue workflow

## Step 1 — Detect
Record how the problem was found:
- log
- alert
- user complaint
- dashboard
- manual observation
- failed scheduled job
- integration failure

## Step 2 — Triage
Determine quickly:
- Is immediate stop-loss required?
- Is rollback required?
- Is feature disablement enough?
- Is manual override needed?

## Step 3 — Stabilize
The first goal is to stop damage and restore control.
Possible actions:
- disable feature
- switch to previous version
- stop the loop
- pause the scheduler
- isolate the connector
- reduce blast radius

Do not continue rollout while actively triaging a serious issue.

## Step 4 — Analyze
Capture:
- trigger
- visible symptom
- immediate technical cause
- deeper enabling condition
- why it was not caught earlier

## Step 5 — Fix
Separate:
- temporary mitigation
- permanent fix

## Step 6 — Improve the system
Every incident must create at least one durable improvement:
- new test
- stronger review checklist
- monitoring improvement
- design correction
- rule addition
- runbook update

## Step 7 — Verify closure
Confirm:
- fix is implemented
- the anti-recurrence action is also implemented
- the change actually reduces recurrence risk

---

## 6. Root cause analysis guidance

Do not stop at the surface symptom.

At minimum, capture:
1. What happened?
2. What triggered it?
3. What allowed it to spread or persist?
4. Why did existing tests/review/monitoring miss it?
5. What must change so it is less likely next time?

---

## 7. Minimal anti-recurrence policy

For small projects, the minimum acceptable incident closure is:

**runtime problem -> restore control -> permanent fix -> one durable improvement**

Durable improvements may be:
- a new test case
- a new checklist item
- a new monitoring signal
- a runbook note
- a new risk escalation rule
- a design restriction

"Fixed in code only" is not enough.

---

## 8. Incident card structure

Suggested fields:
- incident_id
- date/time
- severity
- discovered_from
- impact
- affected_scope
- symptom
- trigger
- temporary_mitigation
- permanent_fix
- root_cause
- why_not_caught
- followup_actions
- owner
- status

---

## 9. Common anti-patterns

The control agent must avoid:
- treating logs as the fix
- stopping at "bad model output"
- blaming the prompt without improving control logic
- patching only the current manifestation
- reopening the same class of issue without new safeguards
- calling an incident closed without follow-up completion

---

## 10. What good closure looks like

A good closure looks like:
- the system is stable again
- the cause is documented clearly enough to reuse
- at least one lasting control was added
- the project is a bit harder to break in the same way again
