# Release Runbook

## Purpose
Use this runbook for small, persistent projects before and during release.

## Before release
- confirm task classification and governance template
- confirm validation results exist
- confirm rollback method is known
- confirm old version is still available
- confirm observation items are known

## During release
- deploy using the chosen method
- watch key logs / results
- verify core path manually if appropriate

## After release
- confirm the main task still succeeds
- confirm error behavior did not worsen
- confirm dependency failures did not spike
- record release outcome

## Stop and roll back if
- core path fails
- error volume increases sharply
- state/data behavior is suspicious
- rollback is safer than continued observation
