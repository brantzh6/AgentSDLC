# Stage Gates Reference Card

Quick-reference for all lifecycle stage gates, gate profiles, and tier applicability.
For full details, see `governance/LIFECYCLE_CONTRACT.md`.

## Minimum Gates by Governance Template

All 7 gates always apply. The profile determines the form, not the existence.

| Gate | Name               | G-Lite (T1)          | G-Std (T2)           | G-Full (T3)         |
|------|--------------------|----------------------|----------------------|---------------------|
| G1   | Design Complete    | Lite brief           | Standard brief       | Full brief + alts   |
| G2   | Design Approved    | Self-check           | Role-switch review   | Full review         |
| G3   | Implementation Done| Required             | Required             | Required            |
| G4   | Code Review Passed | Self-check           | Role-switch review   | Full review         |
| G5   | Testing Passed     | Focused check        | Standard tests       | Full test suite     |
| G6   | Production Deployed| Simple deploy        | Staged deploy        | Full promotion chain|
| G7   | Monitoring Active  | Basic health         | Standard monitoring  | Full monitoring     |

## Gate Profiles

Gate profiles bridge governance tiers to concrete gate requirements.
The tier selects the profile; the profile defines what each gate demands.

| Profile  | Maps To | Philosophy                                    | When to Use                              |
|----------|---------|-----------------------------------------------|------------------------------------------|
| **G-Lite** | T1    | Minimal viable governance                     | One-off, low-risk, easily reversible     |
| **G-Std**  | T2    | Balanced governance for persistent work       | Most L2 work, standard risk              |
| **G-Full** | T3    | Maximum governance for high-risk changes      | L2+R3, L3, Class C/D                     |

**Class override**: Change class can force a higher profile regardless of
base tier. See `governance/LIFECYCLE_CONTRACT.md` Section 3.4.

### Profile Selection Algorithm

```
1. Determine Project Level (L1/L2/L3)
2. Determine Project Type (A/B/C/D)
3. Determine Change Risk (R1/R2/R3)
4. Determine Change Class (A/B/C/D)
5. Select base template from Level + Risk -> T1/T2/T3
6. Apply class override (Class B->min T2, C->min T2/T3, D->force T3)
7. Map template to gate profile (T1->G-Lite, T2->G-Std, T3->G-Full)
```

---

## Gate G1: Design Complete

| Field           | T1                | T2                     | T3                         |
|-----------------|-------------------|------------------------|----------------------------|
| Required output | Lite brief (5 fields) | Standard brief (8 fields) | Full brief + alternatives |
| Pass condition  | Goal + risk + rollback | All 8 fields populated | All T2 + current state + options |
| Decision by     | Controller        | Controller             | Controller                 |

### T1 Lite Brief Fields

1. `goal`
2. `non_goals`
3. `main_risk`
4. `validation_approach`
5. `rollback_note`

### T2 Standard Brief Fields

1. `task_id`
2. `change_class` (A / B / C / D)
3. `problem_statement`
4. `proposed_approach`
5. `scope_boundary` (in-scope + out-of-scope)
6. `affected_files`
7. `constraints`
8. `success_criteria`
9. `stop_conditions`

### T3 Full Brief: All T2 fields plus

9. `current_state_analysis`
10. `alternative_approaches` (min 2)
11. `trade_offs_per_approach`
12. `monitoring_strategy`
13. `failure_handling_strategy`

---

## Gate G2: Design Approved

| Field           | T1                   | T2                      | T3                     |
|-----------------|----------------------|-------------------------|------------------------|
| Review type     | Self-check           | Role-switching review   | Full independent review|
| Pass condition  | No obvious flaws     | Explicit approve result | Findings addressed     |
| Decision by     | Controller           | Controller              | Controller             |

### T1 Self-Check Questions

- Understood correctly?
- Simpler approach available?
- Hidden assumptions?
- Over-designed?

### T2/T3 Review Result Fields

1. `findings` (ordered by severity)
2. `open_questions`
3. `risk_assessment` (T3)
4. `recommendation` (approve / conditional_approve / reject)

---

## Gate G3: Implementation Complete

| Field           | All Tiers                                            |
|-----------------|------------------------------------------------------|
| Required output | Implementation result with summary, files, validation |
| Pass condition  | Validation executed, no placeholders                  |
| Decision by     | Controller                                            |

### Implementation Result Fields

1. `task_id`
2. `change_id`
3. `parent_artifact` (link to approved design brief)
4. `summary`
5. `files_changed`
6. `validation_run`
7. `known_risks`
8. `recommendation` (accept / accept_with_changes / reject / blocked)

---

## Gate G4: Code Review Passed

| Field           | T1                   | T2                      | T3                     |
|-----------------|----------------------|-------------------------|------------------------|
| Review type     | Self-check           | Role-switching review   | Full review            |
| Pass condition  | No obvious bugs      | Findings addressed      | All items resolved     |
| Decision by     | Controller           | Controller              | Controller             |

### Review Priority Order

1. Behavioral regressions
2. Incorrect assumptions about data / API / state
3. Scope creep beyond the design brief
4. Missing validation
5. Security or stability concerns
6. State machine closure (T3)
7. Retry/timeout/fallback safety (T3)

---

## Gate G5: Testing Passed

| Field           | T1                   | T2                      | T3                     |
|-----------------|----------------------|-------------------------|------------------------|
| Test scope      | Focused check        | Standard suite          | Full suite + integration|
| Pass condition  | Main path + 1 failure| No regressions + focused| All levels for class   |
| Decision by     | Controller           | Controller              | Controller             |

### Required Test Levels by Change Class (T2/T3)

| Change Class | Compile | Unit Tests | Integration | Smoke | Migration | Rollback Drill |
|-------------|---------|------------|-------------|-------|-----------|----------------|
| A           | Yes     | Focused    | --          | --    | --        | --             |
| B           | Yes     | Focused    | Yes         | Yes   | --        | --             |
| C           | Yes     | Focused    | Yes         | Yes   | Yes       | Feasibility    |
| D           | Yes     | Focused    | Yes         | Yes   | Yes       | Full drill     |

---

## Gate G6: Production Deployed

| Field           | T1                   | T2                      | T3                     |
|-----------------|----------------------|-------------------------|------------------------|
| Deploy method   | Direct deploy        | Staged (dev->stg->prod) | Full promotion chain   |
| Pass condition  | Main path works      | Staging + prod validated| Full verification      |
| Decision by     | Controller           | Controller              | Controller             |

### T2/T3 Pre-Promotion Checklist

1. Scope frozen
2. Review complete
3. Validation complete with evidence
4. Git checkpoint exists
5. Rollback pointer exists
6. Environment target explicit
7. Backup readiness confirmed (T3)

---

## Gate G7: Monitoring Active (Continuous)

| Field           | T1                   | T2                      | T3                     |
|-----------------|----------------------|-------------------------|------------------------|
| Monitoring scope| Basic health         | Standard checks         | Full monitoring layers |
| Trigger         | Manual observation OK| Automated checks        | Automated + canaries   |
| Decision by     | Controller           | Automated + controller  | Automated + controller |

### Monitoring Layers by Tier

| Layer                  | T1          | T2              | T3                |
|------------------------|-------------|-----------------|-------------------|
| Health checks          | Manual/basic| Every few min   | Every 1 min       |
| Log analysis           | Manual      | Periodic        | Every 15-60 min   |
| Self-test/smoke        | --          | Every 10-30 min | Every 10-30 min   |
| Performance tracking   | --          | --              | Every 5 min       |
| Daily reports          | --          | Optional        | Required          |

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
1. Classify: What is the project level, type, risk, change class, and gate profile?
   -> T1 / T2 / T3

2. Design brief complete (per tier requirements)?
   NO  -> Fix brief (stay at G1)
   YES -> G1 passed

3. Design reviewed (per tier: self-check / role-switch / full)?
   NO  -> Fix or re-design (back to G1)
   YES -> G2 passed -> implement

4. Implementation result complete with validation?
   NO  -> Complete implementation (stay at G3)
   YES -> G3 passed

5. Code review passed (per tier)?
   NO  -> Fix issues (back to G3) or re-design (back to G1)
   YES -> G4 passed -> test

6. Tests passed (per tier and change class)?
   NO  -> Fix and re-test (back to G3)
   YES -> G5 passed -> deploy

7. Deploy and verify (per tier):
   T1: deploy + check main path
   T2: staging -> validate -> prod -> verify
   T3: full promotion chain with observation window

8. Monitor (per tier):
   T1: basic health awareness
   T2: automated checks
   T3: full monitoring with canaries
   -> findings trigger new G1
```
