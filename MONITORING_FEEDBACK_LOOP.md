# Monitoring Feedback Loop Specification

This document defines the closed-loop mechanism that connects runtime
monitoring (Stage 7) back to design (Stage 1), making the development
lifecycle continuous and self-correcting.

For full lifecycle context, see `AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## 1. Overview

The feedback loop is the mechanism that ensures production issues do not
accumulate silently. Every actionable monitoring finding must either:

- be resolved through the standard lifecycle (Stages 1-6), or
- be explicitly deferred by the controller with a recorded reason.

There is no third option. Monitoring findings cannot be silently ignored.

```
Runtime Monitoring
      |
      v
Signal Classification (critical / warning / degraded / info)
      |
      v
Issue-to-Task Conversion
      |
      +---> Auto-processible? --YES--> Generate minimal design brief
      |                                        |
      +---> Controller escalation? --YES--> Controller triages
      |                                        |
      +---> Incident (SEV-1/2)? --YES--> Hotfix path + post-incident review
      |
      v
Design brief enters Stage 1 (Gate G1)
      |
      v
Standard lifecycle continues (G1 -> G2 -> G3 -> G4 -> G5 -> G6)
      |
      v
Fix deployed to production
      |
      v
Runtime monitoring confirms resolution
```

## 2. Monitoring Signal Classification

### 2.1 Signal Levels

| Level    | Definition                                         | Auto-task | Response Time |
|----------|---------------------------------------------------|-----------|---------------|
| Critical | Service down or critical path broken                | Yes (P0)  | Immediate     |
| Warning  | Degraded behavior, recurring errors, canary failure | Yes (P1)  | Within 1 hour |
| Degraded | Metrics below threshold, quality regression         | Yes (P2)  | Within 1 day  |
| Info     | Trend observation, no immediate impact              | No        | Next cycle    |

### 2.2 Critical Path Identifiers

The following are always treated as critical-path signals:

- Service health endpoint failure (`/health` returns non-200)
- Primary chat/API canary failure
- Frontend page load failure
- Browser UI probe failure
- Database connection failure
- Cache/queue connection failure

### 2.3 Signal Sources

| Source                  | Check Type       | Interval       | Signal Level Range    |
|------------------------|------------------|----------------|-----------------------|
| Health endpoint probe  | HTTP status      | Every 1 min    | Critical / Info       |
| Chat canary            | End-to-end test  | Every 10 min   | Critical / Warning    |
| UI browser probe       | Headless render  | Every 30 min   | Critical / Warning    |
| Log pattern analysis   | Error detection  | Every 15 min   | Warning / Degraded    |
| Performance tracker    | Latency/throughput | Every 5 min  | Warning / Degraded    |
| Self-test suite        | Smoke tests      | Every 10 min   | Critical / Warning    |
| Collection health      | Data production  | Every 2 min    | Warning / Degraded    |
| Source plan review     | Quality check    | Every 5 min    | Warning / Degraded    |
| Daily aggregation      | Report           | Once per day   | Info / Degraded       |

## 3. Issue-to-Task Conversion

### 3.1 Conversion Rules

When a monitoring signal at level `critical`, `warning`, or `degraded` is
detected:

1. **Check for duplicates**: search for an existing open task with the same
   `source_type` and `source_id`. If found, update the existing task with
   the latest evidence instead of creating a new one.

2. **Create structured task** with these fields:

   ```
   source:           <monitoring system identifier>
   source_type:      <signal source category>
   source_id:        <unique identifier for the signal>
   signal_level:     critical | warning | degraded
   evidence:         <log entries, health snapshots, metric values>
   affected_surface: <component, route, or path impacted>
   auto_processible: true | false
   priority:         P0 | P1 | P2
   category:         reliability | performance | quality | security
   ```

3. **Priority mapping**:
   - critical -> P0
   - warning -> P1
   - degraded -> P2

4. **Auto-processible determination**: a task is auto-processible only when
   ALL of the following are true:
   - the signal level is `critical`
   - the category is `performance` or `reliability`
   - the corrective action is well-understood and bounded
   - the corrective action does not require controller-level judgment
   - the action does not modify governance rules or agent boundaries

### 3.2 Info-Level Handling

Info-level signals do NOT automatically create tasks. They are:

- logged for trend analysis
- included in daily reports
- available for controller review during planning cycles

The controller may manually create tasks from info signals if warranted.

## 4. Task-to-Design-Brief Conversion

### 4.1 Three Paths

Once a monitoring task exists, it enters the lifecycle through one of three
paths:

#### Path A: Auto-Processible (Bounded Automation)

For well-understood, bounded issues where automated correction is safe:

1. System generates a minimal design brief:
   - `problem_statement`: derived from monitoring evidence
   - `proposed_approach`: known corrective action
   - `scope_boundary`: narrowly bounded to the affected surface
   - `change_class`: usually Class A or B
   - `constraints`: must not change unrelated behavior
   - `success_criteria`: monitoring signal returns to healthy
   - `stop_conditions`: if fix requires broader scope, escalate

2. The brief is submitted to Gate G2 (controller approval).
   - Even auto-processible tasks require controller approval of the brief.
   - The controller may approve, modify, or reject the auto-generated brief.

3. If approved, proceed through the standard lifecycle (G3 -> G4 -> G5 -> G6).

#### Path B: Controller-Escalated (Manual Triage)

For issues requiring human judgment:

1. The monitoring task is presented to the controller.
2. Controller decides one of:
   - **Ignore**: mark as acknowledged, no action (must record reason)
   - **Defer**: schedule for future cycle (must record target date)
   - **Design**: create a design brief and enter Stage 1

3. If design is chosen, the controller creates the brief manually or
   directs the architect-agent to draft it.

4. Standard lifecycle applies from Stage 1.

#### Path C: Incident Response (Hotfix)

For critical production failures (SEV-1 or SEV-2):

1. Immediate bounded corrective action is authorized:
   - rollback to last known good state, OR
   - apply a narrowly-scoped hotfix

2. The hotfix still requires an abbreviated artifact chain:
   - implementation result (summary, files changed, validation run)
   - abbreviated code review (controller verifies no scope creep)
   - focused test (confirm fix, confirm no new regressions)
   - post-deployment verification

3. Within 24 hours (SEV-1) or 48 hours (SEV-2), a full post-incident
   review must be completed.

4. The post-incident review generates a design brief for permanent fix
   and preventive measures, which enters Stage 1 as a standard change.

### 4.2 Path Selection Rules

| Condition                                           | Path                    |
|-----------------------------------------------------|-------------------------|
| Auto-processible = true AND signal = critical       | Path A (auto + G2 gate) |
| Auto-processible = false AND signal != critical     | Path B (controller)     |
| Signal = critical AND service down                  | Path C (incident)       |
| Signal = critical AND service degraded but running  | Path B or C (controller decides) |
| Signal = warning                                    | Path B (controller)     |
| Signal = degraded                                   | Path B (controller)     |

## 5. Incident Severity and Response SLAs

### 5.1 Severity Definitions

| Severity | Impact                                              | Examples                          |
|----------|-----------------------------------------------------|-----------------------------------|
| SEV-1    | Production fully down, all users affected            | API returns 5xx on all endpoints  |
| SEV-2    | Critical path broken, partial functionality remains  | Chat broken but API responds      |
| SEV-3    | Degraded quality or performance, users notice        | 3x latency, quality drops         |
| SEV-4    | Minor anomaly, users unaffected                      | Warning patterns in logs          |

### 5.2 Response SLAs

| Severity | Detection    | First Response | Corrective Action | Post-Incident Review |
|----------|-------------|----------------|--------------------|-----------------------|
| SEV-1    | < 5 min     | Immediate      | < 30 min           | Within 24 hours       |
| SEV-2    | < 15 min    | < 1 hour       | < 4 hours          | Within 48 hours       |
| SEV-3    | < 1 hour    | < 4 hours      | < 1 business day   | Optional (recommended)|
| SEV-4    | < 24 hours  | Next cycle     | Next planning cycle | Not required          |

### 5.3 Escalation Rules

- If SEV-1 is not resolved within 30 minutes, escalate to human operator.
- If SEV-2 is not resolved within 4 hours, escalate to human operator.
- If an issue recurs 3 times within 7 days, auto-escalate severity by one level.
- If a monitoring signal type has never been seen before, classify as at
  least SEV-3 until the controller determines actual severity.

## 6. Post-Incident Review Requirements

### 6.1 Required for SEV-1 and SEV-2

Every SEV-1 and SEV-2 incident must produce a post-incident review document:

```
## Post-Incident Review

### Timeline
- <timestamp>: <event description>
- ...

### Root Cause
<Why the incident occurred>

### Detection
- How it was detected: <monitoring signal that triggered the response>
- Detection latency: <time from incident start to detection>
- Detection gap (if any): <what monitoring should have caught it sooner>

### Corrective Action
- Immediate action: <hotfix or rollback description>
- Files changed: <list>
- Validation: <what was verified>

### Preventive Measures
- Design changes needed: <description>
- Test additions needed: <description>
- Monitoring improvements: <new checks, thresholds, or alerts>

### Follow-Up Tasks
- <task 1 description> (enters lifecycle as Class <X> change)
- <task 2 description> (enters lifecycle as Class <X> change)
- ...
```

### 6.2 Re-Entry into Lifecycle

Each follow-up task from a post-incident review enters the lifecycle as a
standard change:

- Design changes -> Class B or C (standard lifecycle, all gates)
- Test additions -> Class A (lightweight brief + implementation + review)
- Monitoring improvements -> Class A or B depending on scope

### 6.3 Recurrence Prevention

If the same incident class occurs more than once:

1. The second post-incident review must explicitly reference the first.
2. The second review must explain why the first preventive measures were
   insufficient.
3. The controller must decide whether to escalate the change class of
   the preventive measures.

## 7. Monitoring State Management

### 7.1 State Persistence

Monitoring state must be persisted to durable storage:

- health snapshots written to state files after every check cycle
- issue history maintained in the task database
- alert history maintained for trend analysis
- monitoring configuration versioned with project config

### 7.2 Recovery After Restart

After a service restart:

1. Monitoring must resume within one check interval.
2. The last known health snapshot must be loaded from persisted state.
3. If the persisted state is stale (older than 5x the check interval),
   treat the current state as unknown and run a full health check.

### 7.3 Monitoring Self-Check

The monitoring system itself must be monitored:

- If no health check has run for 3x the configured interval, the
  watchdog must restart the monitoring component.
- Monitoring failures must not be silent; they must produce their own
  structured issues.

## 8. Feedback Loop Integrity Rules

### 8.1 Traceability

Every corrective action must be traceable back to the originating monitoring
signal:

- the design brief must reference the monitoring task ID
- the implementation result must reference the design brief
- the promotion record must reference the full chain

### 8.2 No Silent Modifications

No monitoring finding may directly modify production code without passing
through at least an abbreviated stage chain (implementation result +
abbreviated review + validation + deployment verification).

### 8.3 No Self-Acceptance

Auto-evolution or self-modification proposals triggered by monitoring must
go through the full stage chain. They may not self-accept at any gate.

### 8.4 Resolution Verification

After a corrective action is deployed:

1. The monitoring system must verify that the originating signal has
   returned to healthy status.
2. If the signal persists, the task remains open and is re-escalated.
3. Resolution is confirmed only when the monitoring signal is healthy
   for at least 3 consecutive check cycles.

### 8.5 Feedback Loop Metrics

The following metrics should be tracked to measure loop health:

- Mean time to detect (MTTD): time from incident to monitoring signal
- Mean time to respond (MTTR): time from signal to first corrective action
- Mean time to resolve: time from signal to confirmed resolution
- Recurrence rate: percentage of incidents that recur within 30 days
- False positive rate: percentage of signals that required no action
- Loop completion rate: percentage of monitoring tasks that complete the
  full lifecycle (Stage 1 through Stage 6)
