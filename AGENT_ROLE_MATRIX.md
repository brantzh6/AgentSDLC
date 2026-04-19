# Agent Role Matrix

This document maps each agent role to lifecycle stages, defines decision
authority, handoff protocols, and escalation rules.

For full lifecycle context, see `AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## 1. Role Inventory

| Role               | Primary Function                               | Default Mode          |
|--------------------|------------------------------------------------|-----------------------|
| Controller         | Coordinate lifecycle, make gate decisions       | Coordinator (not coder) |
| Architect Agent    | Design analysis, option comparison, risk review | Advisory              |
| Coding Agent       | Bounded code implementation                     | Delegate              |
| Code Review Agent  | Patch review, regression detection              | Advisory              |
| Test Fixer Agent   | Test repair, test addition, validation          | Delegate              |
| Refactor Agent     | Structural cleanup without behavior change      | Delegate              |
| Monitoring System  | Continuous production health verification       | Automated             |

## 2. Stage Participation Matrix

Shows which roles participate at each stage and in what capacity.

| Stage                  | Controller | Architect | Coding | Code Review | Test Fixer | Refactor | Monitoring |
|------------------------|-----------|-----------|--------|-------------|------------|----------|------------|
| 1. Design              | LEAD      | EXECUTE   | --     | --          | --         | --       | TRIGGER    |
| 2. Design Review       | DECIDE    | EXECUTE   | --     | --          | --         | --       | --         |
| 3. Code Implementation | OVERSEE   | --        | EXECUTE| --          | --         | --       | --         |
| 4. Code Review         | DECIDE    | --        | --     | EXECUTE     | --         | --       | --         |
| 5. Testing             | DECIDE    | --        | --     | --          | EXECUTE    | --       | --         |
| 6. Go-to-Production    | LEAD      | --        | --     | --          | --         | --       | VERIFY     |
| 7. Runtime Monitoring  | ESCALATE  | --        | --     | --          | --         | --       | EXECUTE    |

Legend:
- **LEAD**: owns the stage, drives the work
- **EXECUTE**: performs the stage's primary activity
- **DECIDE**: makes the gate pass/fail decision
- **OVERSEE**: monitors execution but does not do the work
- **TRIGGER**: provides input that initiates the stage
- **VERIFY**: performs post-action validation
- **ESCALATE**: receives escalations from the stage
- **--**: not involved

## 3. Decision Authority Matrix

For each gate, who recommends and who decides.

| Gate | Name                | Recommender            | Decider     | Veto Power   |
|------|---------------------|------------------------|-------------|--------------|
| G1   | Design Complete     | Architect Agent        | Controller  | Controller   |
| G2   | Design Approved     | Architect Agent        | Controller  | Controller   |
| G3   | Implementation Done | Coding Agent           | Controller  | Controller   |
| G4   | Code Review Passed  | Code Review Agent      | Controller  | Controller   |
| G5   | Testing Passed      | Test Fixer Agent       | Controller  | Controller   |
| G6   | Production Deployed | Controller             | Controller  | Controller   |
| G7   | Monitoring Active   | Monitoring System      | Automated   | Controller   |

Key principle: **Delegates recommend. Only the controller decides.**

No delegate agent may:
- accept its own output
- bypass the controller's gate decision
- promote changes without controller approval

## 4. Handoff Protocols

### 4.1 Stage 1 -> Stage 2 (Design -> Design Review)

Handoff artifact: **Design Brief**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Design brief       | Architect Agent           | Architect Agent (review)|
| Change class       | Controller                | Architect Agent (review)|
| Context references | Controller                | Architect Agent (review)|

Protocol:
1. Architect agent (or controller) produces the design brief.
2. Controller verifies G1 criteria are met.
3. Controller submits brief for design review.
4. A different architect agent instance (or the same with review role)
   produces the design review result.

### 4.2 Stage 2 -> Stage 3 (Design Review -> Implementation)

Handoff artifact: **Approved Design Brief + Review Result**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Approved brief     | Controller (via G2)       | Coding Agent            |
| Review findings    | Architect Agent           | Coding Agent (context)  |
| Scope constraints  | Design brief              | Coding Agent            |

Protocol:
1. Controller makes G2 decision.
2. If approved_with_changes, controller updates the brief first.
3. Controller creates implementation task with the approved brief.
4. Coding agent receives brief, context, and role contract.

### 4.3 Stage 3 -> Stage 4 (Implementation -> Code Review)

Handoff artifact: **Implementation Result + Changed Files**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Implementation result | Coding Agent           | Code Review Agent       |
| Design brief       | From Stage 2              | Code Review Agent       |
| Changed files      | Coding Agent              | Code Review Agent       |

Protocol:
1. Coding agent produces implementation result.
2. Controller verifies G3 criteria (no placeholders, validation run).
3. Controller creates review task with implementation result and changed files.
4. Code review agent receives brief, implementation result, and files.

### 4.4 Stage 4 -> Stage 5 (Code Review -> Testing)

Handoff artifact: **Code Review Result + Accepted Implementation**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Review result      | Code Review Agent         | Test Fixer Agent        |
| Accepted code      | From Stage 3              | Test Fixer Agent        |
| Test requirements  | Design brief (change class) | Test Fixer Agent      |

Protocol:
1. Code review agent produces review result.
2. Controller makes G4 decision.
3. If accepted_with_changes, coding agent fixes and review re-runs.
4. Controller creates testing task with the accepted implementation.
5. Test fixer agent receives code, review result, and test level requirements.

### 4.5 Stage 5 -> Stage 6 (Testing -> Go-to-Production)

Handoff artifact: **Test Result + Full Artifact Chain**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Test result        | Test Fixer Agent          | Controller              |
| All prior artifacts| Accumulated               | Controller              |
| Pre-promotion checklist | Controller            | Controller              |

Protocol:
1. Test fixer agent produces test result.
2. Controller makes G5 decision.
3. Controller runs pre-promotion checklist.
4. Controller manages staging deployment and validation.
5. Controller manages production promotion.

### 4.6 Stage 6 -> Stage 7 (Go-to-Production -> Monitoring)

Handoff artifact: **Promotion Record**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Promotion record   | Controller                | Monitoring System       |
| Baseline metrics   | Pre-promotion state       | Monitoring System       |
| Regression surface | Design brief              | Monitoring System       |

Protocol:
1. Controller completes promotion and post-promotion verification.
2. Monitoring system receives updated baseline.
3. Monitoring system begins tracking the newly deployed surface.

### 4.7 Stage 7 -> Stage 1 (Monitoring -> Design) -- The Feedback Loop

Handoff artifact: **Monitoring Task**

| Field              | Source                    | Destination             |
|--------------------|--------------------------|-------------------------|
| Monitoring task    | Monitoring System         | Controller              |
| Signal evidence    | Monitoring System         | Controller / Architect  |
| Suggested action   | Monitoring System         | Controller              |

Protocol:
1. Monitoring system detects actionable finding.
2. Finding is converted to structured monitoring task.
3. Controller triages the task (Path A, B, or C per feedback loop spec).
4. If design is needed, controller creates or delegates design brief.
5. Lifecycle continues from Stage 1.

## 5. Escalation Rules

### 5.1 Delegate-to-Controller Escalation

A delegate agent must escalate to the controller when:

- scope expansion is needed beyond the brief
- blocked by missing data, API, or context
- the task requires architectural judgment
- the task requires files outside the allowed write scope
- the task requires schema, data, or environment changes not in scope

Escalation format:

```
blocker:           <what prevents completion>
what_was_attempted: <what the agent tried>
what_is_missing:    <what would unblock the task>
recommendation:     escalate | defer | abort
```

### 5.2 Controller-to-Human Escalation

The controller must escalate to the human operator when:

- SEV-1 is not resolved within 30 minutes
- SEV-2 is not resolved within 4 hours
- a change requires Class D (self-evolution rule change)
- a decision conflicts with documented architecture corrections
- multiple agents disagree on a critical finding
- rollback is needed but rollback artifacts are missing

### 5.3 Cross-Stage Escalation

If a problem is discovered at a later stage that invalidates an earlier
gate decision:

| Discovered At | Affects Gate | Action                                          |
|--------------|-------------|--------------------------------------------------|
| Code Review  | G2 (Design) | Return to Stage 1, re-design with new findings   |
| Testing      | G3 or G4    | Return to Stage 3 or 4, fix implementation        |
| Go-to-Prod   | G5 (Testing)| Return to Stage 5, add missing test coverage      |
| Monitoring   | Any gate    | Enter feedback loop, create new design brief      |

## 6. Concurrent Work Rules

### 6.1 Parallel Tasks

Multiple bounded tasks may proceed in parallel through the lifecycle if:

- they have non-overlapping scope boundaries
- they do not modify the same files
- they are tracked as separate task IDs
- each independently passes all required gates

### 6.2 Conflict Resolution

If two parallel tasks create a conflict (overlapping file changes, semantic
conflict):

1. Both tasks pause at the current gate.
2. Controller reviews the conflict.
3. Controller decides: merge, resequence, or reject one.
4. The resolved task re-enters the lifecycle at the appropriate gate.

### 6.3 Pipeline Depth Limit

To prevent cognitive overload and reduce error risk:

- no more than 3 tasks should be in active implementation (Stages 3-5)
  simultaneously
- no more than 1 task should be in active promotion (Stage 6) at a time
- the controller should not start new Stage 1 work if the active pipeline
  exceeds the depth limit

## 7. Agent Contract Template

Each agent in the project should have a contract file defining:

```markdown
# <Role Name> Agent

## Purpose
<one-sentence description of what this role does>

## Best Fit
<list of task types this role handles>

## Do
<list of expected behaviors>

## Do Not
<list of prohibited behaviors>

## Required Output
<list of fields the agent must return>

## Escalation Triggers
<conditions that require escalating to controller>
```

## 8. Audit and Compliance

### 8.1 Audit Trail

Every lifecycle execution must produce a traceable audit trail:

1. Design brief -> design review result -> implementation result ->
   code review result -> test result -> promotion record

2. Each artifact must reference its predecessor by task ID.

3. The full chain must be recoverable from the promotion record alone.

### 8.2 Compliance Checks

The controller should periodically verify:

- all active tasks have properly formatted artifacts at their current stage
- no task has been in a single stage for longer than its SLA
- no promotion was made without all required gate artifacts
- all monitoring findings have been triaged (not silently ignored)

### 8.3 Lifecycle Health Metrics

Track these metrics to assess lifecycle governance health:

| Metric                        | Target        | Red Flag          |
|-------------------------------|---------------|-------------------|
| Gate skip rate                | 0%            | Any skip          |
| Self-acceptance rate          | 0%            | Any self-accept   |
| Mean stage cycle time         | < 4 hours     | > 24 hours        |
| Promotion without staging     | 0%            | Any direct-to-prod|
| Monitoring task triage rate   | 100%          | < 80%             |
| Post-incident review rate     | 100% (SEV1/2) | Any missing       |
| Rollback readiness rate       | 100%          | < 90%             |
