# Operational Runbooks

Step-by-step guides for release, rollback, and incident handling.
These are action-oriented — follow the steps in order.

For full lifecycle context, see `governance/LIFECYCLE_CONTRACT.md`.

## 1. Release Runbook

### Before Release

1. Confirm task classification, change class, and gate profile.
2. Confirm design review is complete (at minimum self-review for T1).
3. Confirm validation results exist with evidence.
4. Confirm rollback method is known and documented.
5. Confirm old version is still available.
6. Confirm observation items are defined (what to watch after release).
7. For T2+: confirm git checkpoint exists (commit + tag for milestones).
8. For T3: confirm staging validation has passed.

### Pipeline Check

Before promoting, confirm:
- No more than 1 active promotion in progress.
- Pipeline is not overloaded (max 3 active tasks in impl/review/validation).

### During Release

1. Deploy using the chosen method.
2. Watch key logs and results immediately.
3. Verify core path manually if appropriate.
4. For T2+: confirm health endpoint returns healthy.
5. For T3: wait through the defined observation window.

### After Release

1. Confirm the main task still succeeds.
2. Confirm error behavior did not worsen.
3. Confirm dependency failures did not spike.
4. Record release outcome.
5. For T2+: update promotion record.

### Stop and Roll Back If

- core path fails after deployment
- error volume increases sharply
- state/data behavior is suspicious
- rollback is safer than continued observation

## 2. Rollback Runbook

### Trigger Rollback When

- the main path is broken after promotion
- the change creates destructive or unstable behavior
- repeated failure appears after release
- runtime signals clearly worsen compared to prior version
- truth surfaces become ambiguous
- memory or knowledge is unexpectedly polluted

### Rollback Steps

1. **Stop** new rollout activity immediately.
2. **Restore** the previous version:
   - T1: switch back to the old code/script
   - T2: restore code from git checkpoint
   - T3: restore code + config + data if needed
3. **Confirm** service/task stability.
4. **Confirm** the main path works again.
5. **Open** an incident record (use the Incident Closure template).
6. **Do not re-release** until the issue is understood sufficiently.

### After Rollback

1. Document what triggered the rollback.
2. Identify temporary mitigation vs permanent fix needed.
3. Add at least one durable anti-recurrence improvement.
4. The permanent fix enters the lifecycle as a new task.

## 3. Incident Runbook

### Step 1: Detect

Record how the problem was discovered:
- log alert
- monitoring signal
- manual observation
- user complaint
- failed scheduled job
- integration failure
- health check failure

### Step 2: Triage

Determine quickly:
- **Severity**: SEV-1 (down) / SEV-2 (critical broken) / SEV-3 (degraded) / SEV-4 (minor)
- **Immediate action needed?**
  - Is stop-loss required?
  - Is rollback required?
  - Is feature disablement enough?
  - Is manual override needed?

### Step 3: Stabilize

The first goal is to **stop damage and restore control**.

Possible actions:
- disable the broken feature
- switch to previous version (rollback)
- stop the loop/scheduler
- isolate the failing connector
- reduce blast radius

**Do not continue rollout while actively triaging a serious issue.**

### Step 4: Analyze

Capture:
- **trigger**: what started the problem
- **symptom**: what was visible
- **immediate cause**: the direct technical reason
- **deeper condition**: what allowed it to happen
- **why not caught**: why existing tests/review/monitoring missed it

### Step 5: Fix

Separate clearly:
- **temporary mitigation**: what stops the bleeding now
- **permanent fix**: what prevents recurrence

The permanent fix enters the lifecycle as a standard task (classify, design,
implement, review, test, deploy).

### Step 6: Harden

Add at least one **durable improvement**. Choose from:
- new test case
- new monitoring signal or threshold
- stronger review checklist item
- new governance rule or guardrail
- runbook update
- design constraint

"Fixed in code only" is not sufficient closure.

### Step 7: Close

Only close the incident when ALL of:
- [ ] the system is stable
- [ ] the fix is in place
- [ ] the anti-recurrence improvement is also in place
- [ ] for SEV-1/2: post-incident review document is written
- [ ] relevant signal is healthy for 3 consecutive observation cycles

### Recurrence Escalation

If the same issue class recurs 3 times within 7 days:
- auto-escalate severity by one level
- the post-incident review must reference all prior occurrences
- must explain why previous preventive measures were insufficient
- controller must decide whether to escalate the change class

### Post-Incident Review (SEV-1 and SEV-2)

Required within 24h (SEV-1) or 48h (SEV-2):

```md
## Post-Incident Review

### Timeline
- <timestamp>: <event>
- ...

### Root Cause
<why it happened>

### Detection
- How detected: <signal>
- Detection latency: <time from start to detection>
- Detection gap: <what should have caught it sooner>

### Corrective Action
- Immediate: <hotfix or rollback>
- Files changed:
- Validation:

### Preventive Measures
- Design changes:
- Test additions:
- Monitoring improvements:

### Durable Improvement
- Type: test / monitoring / checklist / rule / runbook / design
- Description:
- Status: pending / done

### Follow-Up Tasks
- <task 1> (enters lifecycle as Class _ change)
- <task 2> (enters lifecycle as Class _ change)
```
