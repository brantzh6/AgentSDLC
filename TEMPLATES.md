# Delivery Templates

Fill-in-the-blank templates for structured task execution. Use the shortest
version that still preserves clarity for the selected governance tier.

For full lifecycle context, see `AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## 1. T1 Lightweight Task Template

```md
## Task Classification
- Task ID:
- Project Level: L_
- Project Type: _
- Change Risk: R_
- Change Class: _
- Governance Template: T1
- Gate Profile: G-Lite

## Goal
-

## Non-goals
-

## Main Risk
-

## Plan
-

## Validation
-

## Rollback
-
```

## 2. T2 Standard Task Template

```md
## Task Classification
- Task ID:
- Project Level: L_
- Project Type: _
- Change Risk: R_
- Change Class: _
- Governance Template: T2
- Gate Profile: G-Std

## Background
-

## Goal
-

## Scope / Non-goals
-

## Affected Files
-

## Impacted Modules
-

## Constraints
-

## Success Criteria
-

## Stop Conditions
-

## Plan
-

## Review Notes
-

## Validation Plan
-

## Release / Rollback
-

## Monitoring
-
```

## 3. T3 Reinforced Task Template

```md
## Task Classification
- Task ID:
- Project Level: L_
- Project Type: _
- Change Risk: R_
- Change Class: _
- Governance Template: T3
- Gate Profile: G-Full

## Current State
-

## Goal
-

## Non-goals
-

## Alternative Options
| Option | Approach | Pros | Cons | Risk |
|--------|----------|------|------|------|
| A      |          |      |      |      |
| B      |          |      |      |      |

## Chosen Solution
-

## Risks
-

## Failure Handling
-

## Validation Strategy
-

## Release Strategy
-

## Rollback Strategy
-

## Monitoring Strategy
-

## Review Decision
- approve / conditional_approve / reject

## Closed-loop Tracking
-
```

## 4. Implementation Result Template

```md
## Implementation Result
- Task ID:
- Change ID:
- Parent Artifact: <link to approved design brief>

### Summary
-

### Files Changed
-

### Validation Run
- command:
- result:

### Known Risks
-

### Recommendation
- accept / accept_with_changes / reject / blocked
- Decision by:
```

## 5. Review Result Template

```md
## Review Result
- Task ID:
- Change ID:
- Parent Artifact: <link to implementation result>

### Findings (ordered by severity)
1.

### Open Questions
-

### Validation Gaps
-

### Recommendation
- approve / approve_with_changes / reject
- Decision by:
```

## 6. Test Result Template

```md
## Test Result
- Task ID:
- Change ID:
- Parent Artifact: <link to review result>

### Test Suite
- pass / fail
- total: _ | passed: _ | failed: _ | skipped: _

### New Tests Added
-

### Regression Surface Covered
-

### Known Gaps
-

### Recommendation
- pass / pass_with_gaps / fail
```

## 7. Compact Validation Template

For quick validation notes (T1/T2):

```md
## Validation
- Main path: pass / fail
- Failure path: pass / fail / not tested
- Regression path: pass / fail / not tested
- Not tested:
- Remaining risks:
- Release recommendation: go / hold
```

## 8. Compact Release Template

```md
## Release
- Change summary:
- Deployment method:
- Observation items:
- Stop condition:
- Rollback method:
```

## 9. Promotion Record Template (T2+)

```md
## Promotion Record
- Task ID:
- Change ID:
- Parent Artifact: <link to test result>

### Scope Summary
-

### Build / Version
-

### Promoted From
- (e.g., staging)

### Promoted To
- (e.g., production)

### Review Status
- approved / approved_with_changes

### Pre-Promotion Evidence
- Design review: passed / date
- Code review: passed / date
- Validation: passed / date

### Validation Evidence
-

### Git Checkpoint
- commit:
- tag:

### Rollback Pointer
-

### Backup Status
-

### Monitoring Baseline
- Key signals to watch:
- Expected healthy range:

### Promotion Decision
- accept / accept_with_changes / reject / defer
- Decision by:

### Post-Promotion Verification
- Health endpoint:
- Main path:
- Regression surface:
- Rollback path valid:
- 3 healthy observation cycles: pending / confirmed
```

## 10. Incident Closure Template

```md
## Incident

### Identity
- Incident ID:
- Change ID: (if related to a specific change)
- Date/Time:
- Severity: SEV-1 / SEV-2 / SEV-3 / SEV-4
- Owner:

### Structured Signal
- Source:
- Source Type:
- Source ID:
- Signal Level:
- Category:

### Discovery
- Source:
- Initial Symptom:

### Impact
-

### Trigger
-

### Temporary Mitigation
-

### Permanent Fix
-

### Root Cause
-

### Why It Was Not Caught Earlier
-

### Durable Improvement
- [ ] Type: test / monitoring / checklist / rule / runbook / design
- [ ] Description:
- [ ] Status: pending / done

### Closure Status
- open / mitigated / closed
```

## 11. Rule Card Template

For governance rules learned through incidents:

```md
## Rule Card

### Rule Name
-

### Source
- Learned from: (incident ID / observation / review)

### Trigger Condition
- When:

### Applies To
- Stage:
- Project Type:
- Risk Level:

### Rule Content
-

### Example
-

### Enforcement
- How this rule is checked:
```


## 13. Controller-Coded Exception Template

When the controller directly edits production code instead of delegating:

```md
## Controller-Coded Exception
- Task ID:
- Change ID:

### What Was Changed
-

### Why Delegation Was Not Used
-

### Scope Confirmation
- [ ] Change is small and bounded
- [ ] OR: time-sensitive incident requires immediate action

### Self-Acceptance Prevention
- [ ] Review was performed from a different perspective
- [ ] Review was not rubber-stamped

### Validation
- command:
- result:

### Known Risks
-
```
## 14. Blocked Report Template

```md
## Blocked Report

### Blocker
-

### What Was Attempted
-

### What Is Missing
-

### Recommendation
- escalate / defer / abort
```
