# Stage Gates Reference Card

Quick-reference for all lifecycle stage gates. For full details, see
`AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## Gate G1: Design Complete

| Field           | Requirement                                                         |
|-----------------|---------------------------------------------------------------------|
| Entry criteria  | Clear problem statement or monitoring-generated task exists          |
| Required inputs | Problem statement, scope boundary, change classification            |
| Required output | Design brief with all 8 required fields populated                   |
| Pass condition  | Brief has scope, constraints, success criteria, and change class    |
| Decision by     | Controller                                                          |
| Escalation      | If problem is ambiguous, escalate to human operator before drafting |

### Design Brief Required Fields

1. `problem_statement`
2. `proposed_approach`
3. `scope_boundary` (in-scope + out-of-scope)
4. `affected_files`
5. `constraints`
6. `success_criteria`
7. `stop_conditions`
8. `change_class` (A / B / C / D)

---

## Gate G2: Design Approved

| Field           | Requirement                                                           |
|-----------------|-----------------------------------------------------------------------|
| Entry criteria  | Design brief passes G1                                                |
| Required inputs | Design brief, project mainline context                                |
| Required output | Design review result with findings, risk assessment, recommendation   |
| Pass condition  | Controller explicitly approves or approves_with_changes               |
| Decision by     | Controller (architect-agent recommends)                               |
| Escalation      | If blocked, escalate blocker to human operator                        |

### Design Review Result Required Fields

1. `findings` (ordered by severity)
2. `open_questions`
3. `risk_assessment`
4. `recommendation` (approve / approve_with_changes / reject / blocked)

### Decision Matrix

| Recommendation       | Controller Action                    |
|----------------------|--------------------------------------|
| approve              | Proceed to Stage 3                   |
| approve_with_changes | Update brief, then proceed           |
| reject               | Return to Stage 1 with findings      |
| blocked              | Escalate blocker                     |

---

## Gate G3: Implementation Complete

| Field           | Requirement                                                          |
|-----------------|----------------------------------------------------------------------|
| Entry criteria  | Design brief passes G2                                               |
| Required inputs | Approved design brief, context files                                 |
| Required output | Implementation result with all 6 required fields populated           |
| Pass condition  | Validation executed, no placeholder sections, result is structured   |
| Decision by     | Coding agent signals completion; controller verifies completeness    |
| Escalation      | If blocked, return blocked report with what_was_attempted            |

### Implementation Result Required Fields

1. `summary`
2. `files_changed`
3. `commit_hash`
4. `validation_run`
5. `known_risks`
6. `recommendation` (accept / accept_with_changes / reject / blocked)

### Completeness Check

- All 6 required sections must be populated (not empty or placeholder).
- Validation commands from the design brief must have been executed.
- If blocked, the result must clearly state the blocker.

---

## Gate G4: Code Review Passed

| Field           | Requirement                                                          |
|-----------------|----------------------------------------------------------------------|
| Entry criteria  | Implementation result passes G3                                      |
| Required inputs | Implementation result, design brief, changed files                   |
| Required output | Code review result with findings, validation gaps, recommendation    |
| Pass condition  | Controller explicitly accepts; all blockers resolved                 |
| Decision by     | Controller (code-review-agent recommends)                            |
| Escalation      | If reject, return to Stage 3 or Stage 1 depending on finding scope  |

### Code Review Result Required Fields

1. `findings` (ordered by severity)
2. `open_questions`
3. `validation_gaps`
4. `recommendation` (accept / accept_with_changes / reject)

### Review Priority Order

1. Behavioral regressions (highest priority)
2. Incorrect assumptions about data / API / state
3. Scope creep beyond the design brief
4. Missing validation
5. Security or stability concerns

### Decision Matrix

| Recommendation       | Controller Action                    |
|----------------------|--------------------------------------|
| accept               | Proceed to Stage 5                   |
| accept_with_changes  | Fix issues, re-review, then proceed  |
| reject               | Return to Stage 3 or Stage 1        |

---

## Gate G5: Testing Passed

| Field           | Requirement                                                          |
|-----------------|----------------------------------------------------------------------|
| Entry criteria  | Implementation passes G4                                             |
| Required inputs | Implementation result, changed files, existing test suite            |
| Required output | Test result with suite results, new tests, coverage, gaps            |
| Pass condition  | All required test levels executed, no critical failures              |
| Decision by     | Controller (test-fixer-agent executes)                               |
| Escalation      | If fail, return to Stage 3 with root cause analysis                  |

### Test Result Required Fields

1. `test_suite_result` (pass / fail with counts)
2. `new_tests_added`
3. `regression_surface_covered`
4. `known_gaps`
5. `recommendation` (pass / pass_with_gaps / fail)

### Required Test Levels by Change Class

| Change Class | Compile | Unit Tests | Integration | Smoke | Migration | Rollback Drill |
|-------------|---------|------------|-------------|-------|-----------|----------------|
| A           | Yes     | Focused    | --          | --    | --        | --             |
| B           | Yes     | Focused    | Yes         | Yes   | --        | --             |
| C           | Yes     | Focused    | Yes         | Yes   | Yes       | Feasibility    |
| D           | Yes     | Focused    | Yes         | Yes   | Yes       | Full drill     |

### Decision Matrix

| Recommendation    | Controller Action                              |
|-------------------|------------------------------------------------|
| pass              | Proceed to Stage 6                             |
| pass_with_gaps    | Accept gaps explicitly, then proceed           |
| fail              | Return to Stage 3 with root cause              |

---

## Gate G6: Production Deployed

| Field           | Requirement                                                          |
|-----------------|----------------------------------------------------------------------|
| Entry criteria  | Change passes G5 + pre-promotion checklist satisfied                 |
| Required inputs | All prior artifacts, staging environment, production environment     |
| Required output | Promotion record with all 9 required fields                         |
| Pass condition  | Staging + prod validation passed, rollback path confirmed            |
| Decision by     | Controller                                                           |
| Escalation      | If staging fails, return to Stage 3; if prod fails, trigger rollback |

### Pre-Promotion Checklist

1. Scope frozen
2. Review complete and archived
3. Validation complete with evidence
4. Result documents written
5. Git checkpoint exists (commit + tag)
6. Rollback pointer exists
7. Environment target explicit
8. Backup readiness confirmed
9. Promotion decision explicit from controller

### Promotion Record Required Fields

1. `scope_summary`
2. `review_status`
3. `validation_evidence`
4. `git_checkpoint` (commit + tag)
5. `rollback_pointer`
6. `environment_target`
7. `backup_status`
8. `promotion_decision` (accept / accept_with_changes / reject / defer)
9. `post_promotion_verification`

### Post-Promotion Verification

- Health endpoint checked
- Canonical runtime path checked
- Key regression surface checked
- Rollback path still valid

---

## Gate G7: Monitoring Active (Continuous)

| Field           | Requirement                                                          |
|-----------------|----------------------------------------------------------------------|
| Entry criteria  | Production deployment passes G6                                      |
| Required inputs | Live production environment, monitoring configuration                |
| Required output | Continuous health snapshots, structured issues, alerts               |
| Pass condition  | N/A (continuous); triggers feedback loop when findings detected      |
| Decision by     | Automated (controller for escalations)                               |
| Escalation      | SEV-1/SEV-2 -> immediate controller action                          |

### Monitoring Layers

| Layer                  | Interval       | Scope                                    |
|------------------------|----------------|------------------------------------------|
| Health checks          | Every 1 min    | Service endpoints, critical path canaries |
| Deep log analysis      | Every 15-60 min| Error patterns, performance trends        |
| Self-test loops        | Every 10-30 min| Smoke tests, critical path validation     |
| Scheduled evaluation   | Daily/weekly   | Resource performance, quality assessment  |
| Daily reports          | Once per day   | Aggregated summaries, notifications       |

### Signal-to-Action Mapping

| Signal Level | Priority | Response Time     | Action                           |
|-------------|----------|-------------------|----------------------------------|
| Critical    | P0       | Immediate         | Hotfix or rollback               |
| Warning     | P1       | Within 1 hour     | Create task, design brief        |
| Degraded    | P2       | Within 1 day      | Create task, standard lifecycle  |
| Info        | P3       | Next cycle         | Track, may defer                 |

---

## Quick Decision Flowchart

```
Is design brief complete with all 8 fields?
  NO  -> Fix brief (stay at G1)
  YES -> G1 passed -> submit for design review

Is design review approved by controller?
  NO  -> Fix brief or re-design (back to G1)
  YES -> G2 passed -> delegate implementation

Is implementation result complete with validation?
  NO  -> Complete implementation (stay at G3)
  YES -> G3 passed -> submit for code review

Is code review accepted by controller?
  NO  -> Fix issues (back to G3) or re-design (back to G1)
  YES -> G4 passed -> run tests

Did all required test levels pass?
  NO  -> Fix and re-test (back to G3)
  YES -> G5 passed -> run pre-promotion checklist

Is pre-promotion checklist fully satisfied?
  NO  -> Resolve missing items (stay at G6)
  YES -> Deploy to staging -> validate -> deploy to prod -> verify

Is production healthy?
  NO  -> Trigger rollback or hotfix (SEV-1/SEV-2 path)
  YES -> Monitor continuously -> findings trigger new G1
```
