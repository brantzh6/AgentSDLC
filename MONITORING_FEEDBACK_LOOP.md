# Monitoring Feedback Loop Specification

This document defines the closed-loop mechanism that connects runtime
monitoring (Stage 7) back to design (Stage 1), making the development
lifecycle continuous and self-correcting.

For full lifecycle context, see `AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## Tier Applicability

- **T1**: Basic monitoring. Health awareness and manual observation. The
  feedback loop still applies — issues found manually must still be tracked
  and converted to tasks.
- **T2**: Standard monitoring with automated checks. Full feedback loop
  with structured task conversion.
- **T3**: Full monitoring with canaries, deep analysis, and automated
  signal classification. Full feedback loop with SLA enforcement.

The feedback loop principle is the same for all tiers:
**monitoring findings must not be silently ignored.**

## 1. Overview

The feedback loop ensures production issues do not accumulate silently.
Every actionable monitoring finding must either:

- be resolved through the standard lifecycle (Stages 1-6), or
- be explicitly deferred by the controller with a recorded reason.

There is no third option.

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
Standard lifecycle continues (per governance tier)
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

- Service health endpoint failure
- Primary API/chat canary failure
- Frontend page load failure
- Database connection failure
- Cache/queue connection failure

### 2.3 Signal Sources (T2/T3)

| Source                  | Check Type       | Interval       | Signal Level Range    |
|------------------------|------------------|----------------|-----------------------|
| Health endpoint probe  | HTTP status      | Every 1-5 min  | Critical / Info       |
| API canary             | End-to-end test  | Every 10 min   | Critical / Warning    |
| UI browser probe       | Headless render  | Every 30 min   | Critical / Warning    |
| Log pattern analysis   | Error detection  | Every 15 min   | Warning / Degraded    |
| Performance tracker    | Latency/throughput | Every 5 min  | Warning / Degraded    |
| Self-test suite        | Smoke tests      | Every 10 min   | Critical / Warning    |
| Daily aggregation      | Report           | Once per day   | Info / Degraded       |

For T1 projects, replace automated checks with periodic manual observation.

## 3. Issue-to-Task Conversion

### 3.1 Conversion Rules

When a monitoring signal at level `critical`, `warning`, or `degraded` is
detected:

1. **Check for duplicates**: search for an existing open task with the same
   source and category. If found, update it with latest evidence.

2. **Create structured task**:

   ```
   source:           <monitoring system identifier>
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
   - the corrective action is well-understood and bounded
   - the action does not require controller-level judgment
   - the action does not modify governance rules or agent boundaries

### 3.2 Info-Level Handling

Info-level signals do NOT automatically create tasks. They are:

- logged for trend analysis
- included in daily reports
- available for controller review during planning cycles

## 4. Task-to-Design-Brief Conversion

### 4.1 Three Paths

#### Path A: Auto-Processible (Bounded Automation)

For well-understood, bounded issues:

1. System generates a minimal design brief.
2. The brief is submitted to Gate G2 (controller approval required).
3. If approved, proceed through the gate chain per governance tier.

#### Path B: Controller-Escalated (Manual Triage)

For issues requiring judgment:

1. Controller reviews the monitoring task.
2. Controller decides:
   - **Ignore**: acknowledge, no action (must record reason)
   - **Defer**: schedule for future cycle (must record target date)
   - **Design**: create design brief, enter Stage 1

#### Path C: Incident Response (Hotfix)

For critical production failures (SEV-1 or SEV-2):

1. Immediate bounded corrective action authorized (rollback or hotfix).
2. Hotfix still requires: implementation result, abbreviated review,
   validation, post-deployment verification.
3. Post-incident review within 24h (SEV-1) or 48h (SEV-2).
4. Post-incident review generates design brief for permanent fix.

### 4.2 Path Selection Rules

| Condition                                    | Path                    |
|----------------------------------------------|-------------------------|
| Auto-processible + critical                  | Path A (auto + G2 gate) |
| Not auto-processible + not critical          | Path B (controller)     |
| Critical + service down                      | Path C (incident)       |
| Critical + degraded but running              | Path B or C (controller decides) |
| Warning                                      | Path B (controller)     |
| Degraded                                     | Path B (controller)     |

## 5. Incident Severity and Response SLAs

### 5.1 Severity Definitions

| Severity | Impact                                              |
|----------|-----------------------------------------------------|
| SEV-1    | Production fully down, all users affected            |
| SEV-2    | Critical path broken, partial functionality remains  |
| SEV-3    | Degraded quality or performance, users notice        |
| SEV-4    | Minor anomaly, users unaffected                      |

### 5.2 Response SLAs (T2/T3)

| Severity | Detection    | First Response | Corrective Action | Post-Incident Review |
|----------|-------------|----------------|--------------------|-----------------------|
| SEV-1    | < 5 min     | Immediate      | < 30 min           | Within 24 hours       |
| SEV-2    | < 15 min    | < 1 hour       | < 4 hours          | Within 48 hours       |
| SEV-3    | < 1 hour    | < 4 hours      | < 1 business day   | Optional              |
| SEV-4    | < 24 hours  | Next cycle     | Next planning cycle | Not required          |

For T1 projects, these SLAs are aspirational targets, not strict requirements.

### 5.3 Escalation Rules

- If SEV-1 not resolved within 30 minutes, escalate to human operator.
- If SEV-2 not resolved within 4 hours, escalate to human operator.
- If an issue recurs 3 times within 7 days, auto-escalate severity by one level.

## 6. Post-Incident Review Requirements

### 6.1 Required for SEV-1 and SEV-2

See `RUNBOOKS.md` for the post-incident review template.

Key outputs:

1. Timeline
2. Root cause
3. Detection assessment
4. Corrective action taken
5. Preventive measures
6. At least one durable improvement

### 6.2 Recurrence Prevention

If the same incident class occurs more than once:

1. The second review must reference the first.
2. Must explain why the first preventive measures were insufficient.
3. Controller must decide whether to escalate the change class.

## 7. Feedback Loop Integrity Rules

### 7.1 Traceability

Every corrective action must be traceable back to the originating signal:

- design brief references the monitoring task ID
- implementation result references the design brief
- promotion record references the full chain

### 7.2 No Silent Modifications

No monitoring finding may directly modify production code without passing
through at least an abbreviated stage chain.

### 7.3 No Self-Acceptance

Auto-evolution or self-modification proposals triggered by monitoring must
go through the full stage chain. They may not self-accept.

### 7.4 Resolution Verification

After a corrective action is deployed:

1. Monitoring must verify the originating signal returns to healthy.
2. If the signal persists, the task remains open and is re-escalated.
3. Resolution confirmed when healthy for at least 3 consecutive check cycles.

### 7.5 Feedback Loop Health Metrics

| Metric                        | Target        |
|-------------------------------|---------------|
| Mean time to detect (MTTD)    | Per SLA       |
| Mean time to respond (MTTR)   | Per SLA       |
| Mean time to resolve          | < 24 hours    |
| Recurrence rate (30 days)     | < 10%         |
| False positive rate           | < 20%         |
| Loop completion rate          | 100%          |
| Durable improvement rate      | 1+ per incident |
