# Release Runbook

## Step 1 — confirm readiness
Check:
- classification complete
- review complete
- validation complete
- rollback path defined
- observation points defined

## Step 2 — confirm pipeline capacity
Check:
- no more than 1 active promotion
- promotion is not being forced through overload

## Step 3 — release
Deploy in the chosen way:
- local update
- controlled restart
- staged push
- cautious production promotion

## Step 4 — observe
Check:
- service alive
- errors changed?
- main path works?
- critical dependency healthy?

## Step 5 — decide
If healthy:
- continue observation
If unhealthy:
- stop-loss
- rollback if needed
- open/update structured issue
