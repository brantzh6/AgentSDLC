# Agent Role Matrix

This document defines the agent operating model for the lifecycle.

**Default mode: single controller agent with role-switching.**
Multi-agent delegation is a scale-up path, not the baseline assumption.

For full lifecycle context, see `AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## 1. Single-Agent Operating Mode (Default)

Most personal and small-team AI projects operate with a single controller
agent. This agent handles all lifecycle roles by switching perspectives.

### Role-Switching Sequence

```
Designer -> Reviewer -> Implementer -> Code Reviewer -> Tester -> Operator
```

### How Role-Switching Works

1. **Designer perspective**: Analyze the problem, draft the design brief.
   Focus on scope, constraints, and risks.

2. **Reviewer perspective**: Switch viewpoint. Challenge the design as if
   someone else wrote it. Look for hidden assumptions, simpler alternatives,
   and failure paths.

3. **Implementer perspective**: Write the bounded code change. Stay inside
   the approved brief. Do not broaden scope.

4. **Code reviewer perspective**: Switch viewpoint again. Review the code
   as if someone else wrote it. Check for regressions, scope creep, and
   missing validation.

5. **Tester perspective**: Validate the change. Run tests, check failure
   paths, verify main path works.

6. **Operator perspective**: Deploy, monitor, handle the feedback loop.

### Key Discipline

- When switching to a review role, explicitly challenge the previous role's
  assumptions. Do not rubber-stamp your own work.
- Mark role transitions clearly in output (e.g., "Switching to reviewer
  perspective").
- The review must be genuine, not ceremonial.

### Single-Agent Decision Authority

In single-agent mode, the controller makes all decisions at all gates.
The role-switching discipline replaces the multi-agent recommender/decider
separation.

| Gate | Perspective Used          | Decision       |
|------|--------------------------|----------------|
| G1   | Designer                 | Brief complete |
| G2   | Reviewer                 | Design approved|
| G3   | Implementer              | Code complete  |
| G4   | Code Reviewer            | Review passed  |
| G5   | Tester                   | Tests passed   |
| G6   | Operator                 | Deployed       |
| G7   | Operator (continuous)    | Monitor active |

## 2. Multi-Agent Mode (Scale-Up Path)

When multi-agent coordination becomes technically feasible, the controller
can delegate to specialized agents. This section describes the target model.

### Role Inventory

| Role               | Primary Function                               | Default Mode          |
|--------------------|------------------------------------------------|-----------------------|
| Controller         | Coordinate lifecycle, make gate decisions       | Coordinator           |
| Architect Agent    | Design analysis, option comparison, risk review | Advisory              |
| Coding Agent       | Bounded code implementation                     | Delegate              |
| Code Review Agent  | Patch review, regression detection              | Advisory              |
| Test Fixer Agent   | Test repair, test addition, validation          | Delegate              |
| Refactor Agent     | Structural cleanup without behavior change      | Delegate              |

### Stage Participation Matrix

| Stage                  | Controller | Architect | Coding | Code Review | Test Fixer | Refactor |
|------------------------|-----------|-----------|--------|-------------|------------|----------|
| 1. Design              | LEAD      | EXECUTE   | --     | --          | --         | --       |
| 2. Design Review       | DECIDE    | EXECUTE   | --     | --          | --         | --       |
| 3. Code Implementation | OVERSEE   | --        | EXECUTE| --          | --         | --       |
| 4. Code Review         | DECIDE    | --        | --     | EXECUTE     | --         | --       |
| 5. Testing             | DECIDE    | --        | --     | --          | EXECUTE    | --       |
| 6. Go-to-Production    | LEAD      | --        | --     | --          | --         | --       |
| 7. Runtime Monitoring  | ESCALATE  | --        | --     | --          | --         | --       |

Legend:
- **LEAD**: owns the stage, drives the work
- **EXECUTE**: performs the stage's primary activity
- **DECIDE**: makes the gate pass/fail decision
- **OVERSEE**: monitors execution but does not do the work
- **ESCALATE**: receives escalations from the stage

### Decision Authority Matrix

| Gate | Recommender            | Decider     |
|------|------------------------|-------------|
| G1   | Architect Agent        | Controller  |
| G2   | Architect Agent        | Controller  |
| G3   | Coding Agent           | Controller  |
| G4   | Code Review Agent      | Controller  |
| G5   | Test Fixer Agent       | Controller  |
| G6   | Controller             | Controller  |
| G7   | Monitoring System      | Automated   |

**Key principle: Delegates recommend. Only the controller decides.**

## 3. Handoff Protocols (Multi-Agent Mode)

These protocols define how artifacts pass between agents at each stage
transition. In single-agent mode, these serve as a checklist for what
information to carry forward when switching roles.

### 3.1 Design -> Design Review

- Artifact: Design Brief
- From: Designer (or Architect Agent)
- To: Reviewer (or Architect Agent in review role)
- Must include: brief, change class, context references

### 3.2 Design Review -> Implementation

- Artifact: Approved Design Brief + Review Result
- From: Reviewer
- To: Implementer (or Coding Agent)
- Must include: approved brief, review findings, scope constraints

### 3.3 Implementation -> Code Review

- Artifact: Implementation Result + Changed Files
- From: Implementer (or Coding Agent)
- To: Code Reviewer (or Code Review Agent)
- Must include: implementation result, design brief, changed files

### 3.4 Code Review -> Testing

- Artifact: Review Result + Accepted Implementation
- From: Code Reviewer
- To: Tester (or Test Fixer Agent)
- Must include: review result, accepted code, test requirements

### 3.5 Testing -> Go-to-Production

- Artifact: Test Result + Full Artifact Chain
- From: Tester
- To: Operator (Controller)
- Must include: test result, all prior artifacts, pre-promotion checklist

### 3.6 Go-to-Production -> Monitoring

- Artifact: Promotion Record
- From: Operator (Controller)
- To: Monitoring System
- Must include: promotion record, baseline metrics, regression surface

### 3.7 Monitoring -> Design (Feedback Loop)

- Artifact: Monitoring Task
- From: Monitoring System
- To: Controller / Designer
- Must include: signal evidence, affected surface, suggested action

## 4. Escalation Rules

### 4.1 When to Escalate (Both Modes)

Escalate to a higher authority (controller, or human if controller is stuck):

- scope expansion needed beyond the brief
- blocked by missing data, API, or context
- task requires architectural judgment beyond the brief
- task requires files outside the allowed scope
- task requires schema, data, or environment changes not in scope

### 4.2 Controller-to-Human Escalation

The controller must escalate to the human operator when:

- SEV-1 not resolved within 30 minutes
- SEV-2 not resolved within 4 hours
- a change requires Class D (self-evolution rule change)
- a decision conflicts with documented architecture
- rollback needed but rollback artifacts are missing

### 4.3 Cross-Stage Escalation

If a problem at a later stage invalidates an earlier gate:

| Discovered At | Affects Gate | Action                                 |
|--------------|-------------|----------------------------------------|
| Code Review  | G2 (Design) | Return to Stage 1, re-design           |
| Testing      | G3 or G4    | Return to Stage 3 or 4, fix            |
| Go-to-Prod   | G5 (Testing)| Return to Stage 5, add test coverage   |
| Monitoring   | Any gate    | Enter feedback loop, new design brief  |

## 5. Concurrent Work Rules

### 5.1 Pipeline Depth Limit

To prevent cognitive overload and reduce error risk:

- no more than 3 tasks in active implementation (Stages 3-5) simultaneously
- no more than 1 task in active promotion (Stage 6) at a time
- do not start new design work if the pipeline exceeds the depth limit

### 5.2 Conflict Resolution

If two parallel tasks create a conflict:

1. Both tasks pause at the current gate.
2. Controller reviews the conflict.
3. Controller decides: merge, resequence, or reject one.
4. The resolved task re-enters at the appropriate gate.

## 6. Agent Contract Template

Each agent role (in multi-agent mode) should have a contract:

```md
# <Role Name> Agent

## Purpose
<one-sentence description>

## Best Fit
<list of task types this role handles>

## Do
<expected behaviors>

## Do Not
<prohibited behaviors>

## Required Output
<fields the agent must return>

## Escalation Triggers
<conditions requiring escalation to controller>
```

## 7. Lifecycle Health Metrics

| Metric                        | Target        | Red Flag          |
|-------------------------------|---------------|-------------------|
| Gate skip rate                | 0%            | Any skip          |
| Self-acceptance rate          | 0%            | Any self-accept   |
| Mean stage cycle time         | < 4 hours     | > 24 hours        |
| Promotion without staging     | 0% (T2+)     | Any direct-to-prod|
| Monitoring task triage rate   | 100%          | < 80%             |
| Post-incident review rate     | 100% (SEV1/2) | Any missing       |
| Durable improvement rate      | 100% per incident | < 100%        |
