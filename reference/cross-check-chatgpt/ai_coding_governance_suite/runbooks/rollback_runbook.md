# Rollback Runbook

## Purpose
This runbook defines the minimum rollback behavior for small persistent projects.

## Trigger rollback when
- the main path is broken
- the change creates destructive or unstable behavior
- repeated failure appears after release
- the runtime signal clearly worsens compared with the prior version

## Rollback steps
1. stop new rollout activity
2. restore the previous version or disable the changed path
3. confirm service/task stability
4. confirm the main path works again
5. open an incident record
6. do not re-release until the issue is understood sufficiently

## After rollback
- document what triggered the rollback
- identify temporary mitigation vs permanent fix
- add a durable anti-recurrence improvement
