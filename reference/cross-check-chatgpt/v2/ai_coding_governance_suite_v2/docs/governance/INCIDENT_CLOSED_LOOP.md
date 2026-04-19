# INCIDENT_CLOSED_LOOP.md

## 1. Purpose

This document defines the runtime issue handling and closed-loop improvement model for small or personal AI coding projects.

The objective is not to create heavy incident management bureaucracy.
The objective is to ensure that runtime problems become durable improvements instead of repeated firefighting.

Version 2 adds:
- structured signal-to-task conversion
- duplicate checking
- three handling paths (A/B/C)
- post-hotfix return to the standard lifecycle
- health-based closure discipline

---

## 2. Core principle

A runtime issue is not complete when the service works again.

A runtime issue is complete only when:
1. service is stabilized
2. the issue is converted into structured evidence
3. root cause is understood well enough
4. at least one durable anti-recurrence improvement is made
5. that improvement is tracked to completion
6. post-fix monitoring confirms the issue is actually gone

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

## 5. Signal-to-task conversion

When an actionable runtime signal is detected:

### Step 1 — classify the signal
Capture:
- `source`
- `source_type`
- `source_id`
- `signal_level`
- `evidence`
- `affected_surface`
- `auto_processible`
- `priority`
- `category`

Suggested meanings:
- `source`: monitoring system or origin, e.g. logs, cron, user report, dashboard
- `source_type`: job, route, connector, loop, memory, scheduler, alert, complaint
- `source_id`: unique signal identifier if available
- `signal_level`: critical | warning | degraded | info
- `category`: reliability | performance | quality | security

### Step 2 — check for duplicates
Before creating a new issue:
- search for an open task with the same `source_type` and `source_id`
- if found, update the existing task with fresh evidence
- only create a new incident/task if this is meaningfully distinct

### Step 3 — create the structured issue/task
The structured task should contain at minimum:
- source
- source_type
- source_id
- signal_level
- evidence
- affected_surface
- auto_processible
- priority
- category
- symptom
- impact

Info-level signals do not have to create tasks automatically, but should remain available for trend analysis.

---

## 6. Three handling paths

### Path A — Auto-processible bounded issue
Use only when all are true:
- the issue is well understood
- the corrective action is bounded
- the issue does not require controller-level architectural judgment
- the issue does not modify governance rules, review boundaries, or mutation permissions
- the issue does not require Class D handling

Expected flow:
1. create structured task
2. create lightweight design brief
3. controller approves the bounded fix path
4. standard lifecycle continues from design

### Path B — Controller triage
Use when:
- the issue is not severe enough for emergency hotfix
- judgment is required
- the scope or meaning is not trivial

Expected controller choices:
- create a design brief now
- merge with existing planned work
- defer to next planning cycle
- reject as noise after recording rationale

### Path C — Incident / hotfix
Use for severe failures when immediate stabilization is more important than normal flow speed.

Allowed immediate actions:
- rollback to last known good state
- disable a feature or route
- isolate a connector
- apply a narrowly scoped hotfix

Path C still requires an abbreviated artifact chain:
- implementation summary
- abbreviated review
- focused validation
- post-deployment verification

Then:
- P0/P1 incidents must generate a post-incident review
- the permanent fix and preventive measures must re-enter the lifecycle as standard work

Recommended time targets:
- P0: post-incident review within 24 hours
- P1: post-incident review within 48 hours

---

## 7. Runtime issue workflow

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
- Which path applies: A, B, or C?

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
- the relevant signal is healthy for **3 consecutive observation cycles**, or an explicitly justified equivalent standard

---

## 8. Root cause analysis guidance

Do not stop at the surface symptom.

At minimum, capture:
1. What happened?
2. What triggered it?
3. What allowed it to spread or persist?
4. Why did existing tests/review/monitoring miss it?
5. What must change so it is less likely next time?

---

## 9. Minimal anti-recurrence policy

For small projects, the minimum acceptable closure is:

**runtime problem -> restore control -> permanent fix -> one durable improvement -> healthy post-fix observation**

Durable improvements may be:
- a new test case
- a new checklist item
- a new monitoring signal
- a runbook note
- a new risk escalation rule
- a design restriction

"Fixed in code only" is not enough.

---

## 10. Incident card structure

Suggested fields:
- incident_id
- date/time
- severity
- owner
- source
- source_type
- source_id
- signal_level
- evidence
- affected_surface
- auto_processible
- priority
- category
- impact
- symptom
- trigger
- temporary_mitigation
- permanent_fix
- root_cause
- why_not_caught
- followup_actions
- closure_verification
- status

---

## 11. Common anti-patterns

The control agent must avoid:
- treating logs as the fix
- stopping at "bad model output"
- blaming the prompt without improving control logic
- patching only the current manifestation
- reopening the same class of issue without new safeguards
- calling an incident closed immediately after deploy
- skipping duplicate checks and creating issue spam

---

## 12. What good closure looks like

A good closure looks like:
- the system is stable again
- the issue is traceable to structured evidence
- the cause is documented clearly enough to reuse
- at least one lasting control was added
- the permanent fix returned to the normal lifecycle when appropriate
- the relevant monitoring signal stayed healthy long enough to trust the fix
