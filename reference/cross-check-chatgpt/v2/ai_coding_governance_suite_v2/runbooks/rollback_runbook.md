# Rollback Runbook

## Step 1 — identify rollback trigger
Examples:
- main path broken after release
- severe error spike
- false-success behavior
- memory/state corruption risk
- uncontrolled boundary/authority effect

## Step 2 — identify rollback target
Know:
- last known good version
- required config state
- required data/state restoration if relevant

## Step 3 — execute rollback
Possible actions:
- revert deployment
- restore previous package
- restore config
- disable changed surface
- stop the affected loop or connector

## Step 4 — validate recovery
Check:
- main path works
- severe symptom removed
- critical logs stable

## Step 5 — record and loop back
Capture:
- what was rolled back
- why
- what evidence justified it
- what follow-up task/incident is required
