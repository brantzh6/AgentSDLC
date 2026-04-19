# Incident Runbook

## Purpose
This runbook gives the control agent a compact method for handling runtime issues.

Version 2 adds structured issue conversion and explicit Path A/B/C handling.

---

## Step 1 — detect
Record:
- source
- source_type
- source_id
- signal_level
- evidence
- affected_surface
- category
- impact

If possible, capture the exact signal or log evidence, not just a prose description.

---

## Step 2 — check duplicates
Before opening a new issue/task:
- look for an open task with the same `source_type` and `source_id`
- if found, update that task with fresh evidence
- create a new issue only if this is truly distinct

---

## Step 3 — triage
Decide whether this is:
- Path A: bounded auto-processible issue
- Path B: controller-triaged issue
- Path C: incident / hotfix

Also decide whether to:
- stop
- disable
- roll back
- isolate
- continue with caution

---

## Step 4 — stabilize
Restore control first.
Possible actions:
- rollback
- disable a feature
- stop a loop
- pause a scheduler
- isolate a connector
- reduce blast radius

Do not continue expansion or rollout while the system is unstable.

---

## Step 5 — analyze
Record:
- trigger
- symptom
- impact
- direct cause
- deeper enabling condition
- why earlier controls missed it

---

## Step 6 — fix
Separate:
- temporary mitigation
- permanent correction

If using Path C:
- keep the corrective action narrowly scoped
- still produce an implementation summary
- still run abbreviated review
- still run focused validation
- still verify after deployment

---

## Step 7 — harden
Add at least one durable improvement:
- test
- monitoring
- checklist
- rule
- runbook
- design constraint

If the issue was handled via Path C, create or schedule the permanent-fix task back into the standard lifecycle.

---

## Step 8 — close
Only close when:
- the system is stable
- the permanent fix is in place or formally tracked
- anti-recurrence action is also in place or formally tracked
- the relevant signal is healthy for 3 consecutive observation cycles, or an explicitly justified equivalent
