# AI Coding Lifecycle Governance Contract

Status: baseline
Version: 1.0

## 1. Purpose and Scope

This document is the operating contract for any development controller agent
managing an AI-driven coding project.

It governs:

- how changes move from idea to production
- what gates must be passed at each stage
- who holds decision authority
- how runtime monitoring feeds back into new design work

It does not govern:

- product strategy or vision
- business priorities
- user-facing feature selection

This contract exists to prevent the failure mode where AI coding projects
start fast, accumulate unreviewed changes, and eventually become
unrecoverable and unmaintainable.

## 2. Core Principles

### 2.1 Non-Negotiable Rules

1. The controller agent is the coordinator, not the primary coder.
   - Default mode is writing briefs, constraints, and acceptance criteria.
   - Direct code edits are exceptions, not the normal path.

2. No agent may accept its own work as final.
   - Every output is a candidate until reviewed by the controller.

3. Work must be bounded.
   - One task, one scope, one result.
   - If scope expands during execution, stop and report.

4. Do not invent behavior.
   - If data or API is insufficient, stop and report.
   - Do not fake missing fields or guess missing contracts.

5. Every change must be auditable.
   - Files changed, validation evidence, known risks, and recommendation
     must be returned for every task.

6. Promotion requires evidence.
   - No change enters production because an agent produced it.
   - A change becomes promotable only after passing the full gate chain.

7. Rollback must always be possible.
   - If the project cannot return to a previously trusted state, it is not
     under control.

### 2.2 Operating Defaults

- UTF-8 encoding for all project files.
- Minimal safe patches preferred over large rewrites.
- No dependencies added without explicit approval.
- No architecture decisions made by delegate agents.
- No unrelated fixes mixed into one change unit.

## 3. Lifecycle Stage Definitions

The lifecycle consists of seven stages forming a continuous loop:

```
Design -> Design Review -> Code Implementation -> Code Review -> Testing -> Go-to-Production -> Runtime Monitoring
  ^                                                                                                    |
  |____________________________________________________________________________________________________|
                                          (Feedback Loop)
```

### Stage 1: Design

Purpose: Define what will be built, why, and within what boundaries.

Required activities:

1. Create a design brief containing:
   - problem statement
   - proposed approach
   - scope boundary (what is in, what is out)
   - affected files and surfaces
   - constraints and assumptions
   - success criteria
   - stop conditions (when to abort)

2. For non-trivial changes, produce an option analysis:
   - at least two approaches compared
   - trade-offs explicit
   - risks identified per approach
   - recommended path with justification

3. Classify the change (see Section 6: Change Classification).

Entry criteria:

- a clear problem statement or monitoring-generated task exists
- the change is scoped to a bounded unit

Exit criteria (Gate G1: Design Complete):

- design brief exists with all required fields
- change class is determined
- scope boundary is explicit
- the brief is ready for review

Responsible role: architect-agent or controller

### Stage 2: Design Review

Purpose: Validate the design before any implementation begins.

Required activities:

1. Review the design brief for:
   - correctness of problem framing
   - feasibility of proposed approach
   - completeness of scope boundary
   - risk identification quality
   - alignment with project mainline and architecture

2. Produce a design review result containing:
   - findings ordered by severity
   - open questions
   - risk assessment
   - recommendation: `approve`, `approve_with_changes`, `reject`, `blocked`

3. Controller makes the gate decision:
   - `approve`: proceed to implementation
   - `approve_with_changes`: update brief, then proceed
   - `reject`: return to Stage 1 with findings
   - `blocked`: escalate the blocker

Entry criteria:

- design brief passes Gate G1

Exit criteria (Gate G2: Design Approved):

- design review result exists
- controller has explicitly approved or approved_with_changes
- if approved_with_changes, the brief has been updated

Responsible role: architect-agent (review), controller (decision)

### Stage 3: Code Implementation

Purpose: Produce the bounded code change described in the approved design brief.

Required activities:

1. Read the approved design brief and context files.
2. Make the smallest safe patch that satisfies the brief.
3. Run the validation commands specified in the brief.
4. Produce a structured implementation result containing:
   - summary
   - files changed
   - commit hash (if applicable)
   - validation run output
   - known risks
   - recommendation: `accept`, `accept_with_changes`, `reject`, `blocked`

Safe working pattern:

- stay inside the brief
- do not broaden scope
- do not start new architecture branches
- do not mix unrelated fixes
- do not change backend semantics in UI-only tasks
- if blocked, stop and report instead of guessing

Entry criteria:

- design brief has passed Gate G2

Exit criteria (Gate G3: Implementation Complete):

- implementation result exists with all required sections populated
- validation commands from the brief have been executed
- no placeholder sections remain in the result

Responsible role: coding-agent (implementation), controller (oversight)

### Stage 4: Code Review

Purpose: Verify the implementation for correctness, regressions, and scope
discipline before testing.

Required activities:

1. Review the implementation patch against the design brief for:
   - behavioral regressions
   - incorrect assumptions about data, API, or state
   - scope creep beyond the brief
   - missing validation
   - security or stability concerns

2. Produce a code review result containing:
   - findings ordered by severity
   - open questions
   - validation gaps
   - recommendation: `accept`, `accept_with_changes`, `reject`

3. Distinguish blockers from polish items.

4. Controller makes the gate decision:
   - `accept`: proceed to testing
   - `accept_with_changes`: fix and re-review
   - `reject`: return to Stage 3 or Stage 1 with findings

Entry criteria:

- implementation result passes Gate G3

Exit criteria (Gate G4: Code Review Passed):

- code review result exists
- controller has explicitly accepted or accepted_with_changes
- if accepted_with_changes, fixes have been applied and re-verified

Responsible role: code-review-agent (review), controller (decision)

### Stage 5: Testing

Purpose: Verify the change works correctly and does not regress existing
behavior.

Required activities:

1. Run existing test suite to confirm no regressions.
2. Add or update tests for the changed behavior surface.
3. Run focused tests covering the specific change.
4. For runtime behavior changes (Class B+), run integration or smoke tests.
5. For environment/data changes (Class C+), run migration validation.

Test requirements by change class:

- Class A: compile check + focused unit tests
- Class B: Class A + integration tests + smoke tests
- Class C: Class B + migration validation + rollback feasibility check
- Class D: Class C + explicit rollback drill

Produce a test result containing:

- test suite: pass/fail with counts
- new tests added (list)
- regression surface covered (description)
- known gaps
- recommendation: `pass`, `pass_with_gaps`, `fail`

Entry criteria:

- implementation passes Gate G4

Exit criteria (Gate G5: Testing Passed):

- test result exists
- all required test levels for the change class have been executed
- no critical failures remain
- if pass_with_gaps, gaps are explicitly documented and accepted by controller

Responsible role: test-fixer-agent (execution), controller (acceptance)

### Stage 6: Go-to-Production

Purpose: Promote the validated change through staging into production safely.

Required activities:

1. Pre-promotion checklist:
   - scope is frozen (no late additions)
   - review is complete and archived
   - validation is complete with evidence
   - result documents are written
   - git checkpoint exists (commit + tag if milestone)
   - rollback pointer exists for risky changes
   - environment target is explicit (staging or production)
   - backup or restore readiness confirmed
   - promotion decision is explicit from controller

2. Staging deployment:
   - deploy to staging environment
   - run staging validation (health check, canonical paths, regression surface)
   - confirm no regressions in staging

3. Production promotion (only after staging acceptance):
   - deploy to production environment
   - run post-promotion validation
   - confirm health endpoint, canonical paths, and key surfaces

4. Post-promotion verification:
   - health endpoint checked
   - canonical runtime path checked
   - key regression surface checked
   - rollback path still valid

Promotion order is strictly:

1. development -> staging
2. staging -> production

Direct development-to-production or sandbox-to-production is prohibited.

Entry criteria:

- change passes Gate G5
- all pre-promotion checklist items are satisfied

Exit criteria (Gate G6: Production Deployed):

- staging validation passed
- production deployment completed
- post-promotion verification passed
- rollback path confirmed valid

Responsible role: controller (decision and execution oversight)

### Stage 7: Runtime Monitoring

Purpose: Continuously verify production health and detect issues that require
corrective action.

Required monitoring layers:

1. **Minute-level health checks**
   - service health endpoints
   - critical path canary tests
   - rapid anomaly detection on recent logs

2. **Periodic deep analysis** (every 15-60 minutes)
   - log pattern analysis
   - error trend detection
   - performance regression detection

3. **Self-test loops** (every 10-30 minutes)
   - lightweight smoke tests against live services
   - critical path validation (API health, chat canaries, UI probes)
   - result converted to structured issues with priority classification

4. **Scheduled evaluation** (daily/weekly)
   - source/resource performance evaluation
   - collection health tracking
   - quality assessment of managed resources

5. **Daily reports**
   - aggregated error/warning counts
   - important insights summary
   - notification for critical or degraded states

Monitoring output:

- health snapshots persisted to state files
- structured issues created for detected problems
- alerts triggered for critical/warning conditions
- tasks created for actionable findings

Entry criteria:

- production deployment passes Gate G6

Exit criteria:

- monitoring is continuous; it does not "pass"
- when a monitoring finding requires action, it triggers the feedback loop
  (see Section 4)

Responsible role: monitoring system (automated), controller (escalation decisions)

## 4. The Closed Loop: Monitoring to Design

This is the critical feedback mechanism that makes the lifecycle continuous.

### 4.1 Signal Classification

Monitoring signals are classified into four levels:

| Level    | Meaning                                        | Response Time |
|----------|------------------------------------------------|---------------|
| Critical | Production service down or critical path broken | Immediate     |
| Warning  | Degraded behavior or recurring errors detected  | Within 1 hour |
| Degraded | Quality metrics below threshold                 | Within 1 day  |
| Info     | Trend observation, no immediate action needed   | Next cycle    |

### 4.2 Issue-to-Task Conversion

When monitoring detects an actionable finding:

1. The finding is converted to a structured task containing:
   - source: monitoring system identifier
   - signal level: critical / warning / degraded / info
   - evidence: log entries, health snapshots, metric values
   - affected surface: which component or path is impacted
   - auto-processible: whether automated correction is safe

2. Duplicate detection: if an open task for the same source and category
   already exists, update it instead of creating a new one.

3. Priority mapping:
   - critical -> P0
   - warning -> P1
   - degraded -> P2
   - info -> P3

### 4.3 Task-to-Design-Brief Conversion

Tasks that require code changes re-enter the lifecycle at Stage 1:

1. For auto-processible tasks (bounded, well-understood, safe to automate):
   - generate a minimal design brief automatically
   - the brief must still pass Gate G2 (controller approval)
   - proceed through the full stage chain

2. For controller-escalated tasks:
   - controller reviews the monitoring task
   - controller decides: ignore, defer, or create design brief
   - if design brief created, proceed through the full stage chain

3. For incident-level findings (critical):
   - immediate bounded corrective action is allowed (hotfix path)
   - hotfix still requires: implementation result, abbreviated review,
     validation, and post-deployment verification
   - a full post-incident design review must follow within 24 hours

### 4.4 Feedback Loop Rules

1. No monitoring finding may directly modify production code without
   passing through at least an abbreviated stage chain.

2. Auto-evolution or self-modification proposals must go through the
   full stage chain. They may not self-accept.

3. Every corrective action triggered by monitoring must be archived
   with traceability back to the originating monitoring signal.

4. Post-incident reviews must produce:
   - root cause analysis
   - corrective action taken
   - preventive measures (design changes, test additions, monitoring improvements)
   - updated monitoring rules if the issue class was previously undetected

## 5. Stage Gate Summary

| Gate | Name                  | Key Requirement                           | Decision By  |
|------|-----------------------|-------------------------------------------|--------------|
| G1   | Design Complete       | Brief with scope, constraints, criteria   | Controller   |
| G2   | Design Approved       | Review passed, controller approved        | Controller   |
| G3   | Implementation Done   | Result with validation, no placeholders   | Coding agent |
| G4   | Code Review Passed    | Review findings addressed, accepted       | Controller   |
| G5   | Testing Passed        | Required test levels executed, no fails   | Controller   |
| G6   | Production Deployed   | Staging + prod validation passed          | Controller   |
| G7   | Monitoring Active     | Continuous; triggers loop on findings     | Automated    |

## 6. Change Classification

### Class A: Safe Local Change

Examples: small bug fix, focused route extension, narrow test addition,
documentation correction.

Minimum gates: G1 (lightweight brief) + G3 + G4 + G5 (focused tests) + G6 (if runtime-affecting).

Minimum validation: compile check, focused test, controller review, git archive.

### Class B: Runtime Behavior Change

Examples: new runtime route, state machine change, API behavior change,
controller decision boundary change.

Minimum gates: All (G1 through G6).

Minimum validation: focused tests, integration tests, controller review,
milestone document, release note.

### Class C: Environment / Data / Schema / Memory Change

Examples: database migration, memory contract change, production config change,
knowledge base wiring, release process change.

Minimum gates: All (G1 through G6) with enhanced requirements.

Minimum validation: explicit rollback note, backup readiness, staging
validation, migration check, controller acceptance.

### Class D: Self-Evolution or Auto-Modification Rule Change

Examples: changing what the system may rewrite automatically, changing what
agents may mutate directly, changing review bypass rules.

Minimum gates: All (G1 through G6) with maximum scrutiny.

Minimum validation: explicit controller review, explicit risk note, explicit
rollback plan, no direct production landing, rollback drill.

## 7. Agent Role Boundaries

### 7.1 Controller Agent

The controller agent is the main coordinator. It:

- creates and prioritizes design briefs
- makes all gate decisions (approve/reject/defer)
- does not write production code as its default mode
- reviews all delegate outputs before acceptance
- owns the promotion decision
- manages the monitoring feedback triage

The controller may not:

- delegate its own gate decision authority
- auto-accept its own outputs
- bypass stage gates

### 7.2 Architect Agent

The architect agent supports design analysis. It:

- compares implementation options
- surfaces edge cases and risks
- proposes minimal safe design adjustments
- reviews design briefs for feasibility

The architect agent may not:

- make final architecture decisions
- approve its own designs
- broaden scope beyond the analysis brief

### 7.3 Coding Agent

The coding agent implements bounded tasks. It:

- reads the approved brief and context
- makes the smallest safe patch
- runs required validation
- reports files changed and known risks

The coding agent may not:

- redesign architecture
- broaden scope
- add dependencies without approval
- mix unrelated fixes into one patch

### 7.4 Code Review Agent

The code review agent reviews implementations. It:

- checks for behavioral regressions
- checks for scope creep
- checks for missing validation
- produces findings ordered by severity

The code review agent may not:

- rewrite the patch
- make final acceptance decisions
- broaden the review scope

### 7.5 Test Fixer Agent

The test fixer agent handles test maintenance. It:

- repairs failing tests
- adds tests for changed behavior
- preserves test intent
- explains root cause

The test fixer agent may not:

- weaken assertions to make tests pass
- delete coverage without approval
- change unrelated code paths

### 7.6 Refactor Agent

The refactor agent performs structural cleanup. It:

- reduces duplication
- improves readability
- extracts helpers
- keeps behavior unchanged

The refactor agent may not:

- combine refactors with strategy changes
- silently alter runtime semantics
- expand into broad rewrites

## 8. Delivery Format Requirements

### 8.1 Design Brief (Stage 1)

Required fields:

1. `problem_statement`
2. `proposed_approach`
3. `scope_boundary` (in-scope, out-of-scope)
4. `affected_files`
5. `constraints`
6. `success_criteria`
7. `stop_conditions`
8. `change_class` (A / B / C / D)

### 8.2 Design Review Result (Stage 2)

Required fields:

1. `findings` (ordered by severity)
2. `open_questions`
3. `risk_assessment`
4. `recommendation` (approve / approve_with_changes / reject / blocked)

### 8.3 Implementation Result (Stage 3)

Required fields:

1. `summary`
2. `files_changed`
3. `commit_hash`
4. `validation_run`
5. `known_risks`
6. `recommendation` (accept / accept_with_changes / reject / blocked)

### 8.4 Code Review Result (Stage 4)

Required fields:

1. `findings` (ordered by severity)
2. `open_questions`
3. `validation_gaps`
4. `recommendation` (accept / accept_with_changes / reject)

### 8.5 Test Result (Stage 5)

Required fields:

1. `test_suite_result` (pass / fail with counts)
2. `new_tests_added`
3. `regression_surface_covered`
4. `known_gaps`
5. `recommendation` (pass / pass_with_gaps / fail)

### 8.6 Promotion Record (Stage 6)

Required fields:

1. `scope_summary`
2. `review_status`
3. `validation_evidence`
4. `git_checkpoint` (commit + tag)
5. `rollback_pointer`
6. `environment_target`
7. `backup_status`
8. `promotion_decision` (accept / accept_with_changes / reject / defer)
9. `post_promotion_verification`

### 8.7 Monitoring Task (Stage 7 -> Stage 1)

Required fields:

1. `source` (monitoring system identifier)
2. `signal_level` (critical / warning / degraded / info)
3. `evidence` (log entries, snapshots, metrics)
4. `affected_surface`
5. `auto_processible` (true / false)
6. `priority` (P0 / P1 / P2 / P3)

### 8.8 Blocked Report (Any Stage)

When an agent cannot complete its task:

1. `blocker` (what prevents completion)
2. `what_was_attempted`
3. `what_is_missing`
4. `recommendation` (escalate / defer / abort)

## 9. Forbidden Shortcuts

The following are prohibited at all times:

1. **Chat-only acceptance**: no change is accepted just because it was
   discussed in conversation.

2. **Delegate self-acceptance**: no agent may accept its own output as final.

3. **Direct experimental patch to production**: all changes must go through
   staging first.

4. **Mixed promotion units**: unrelated fixes must not be bundled into one
   promotion.

5. **Self-evolution auto-promotion**: auto-modification proposals must go
   through the full review chain.

6. **Gate skipping**: no stage may be skipped for any change class.

7. **Silent scope broadening**: if scope expands, stop and report.

8. **Validation-free acceptance**: missing validation means
   `accept_with_changes` or `reject`, never silent acceptance.

9. **Undocumented hotfixes**: even emergency fixes must produce an
   implementation result and abbreviated review.

10. **Rollback-free promotion**: if rollback is not possible, the change
    is not promotable.

## 10. Incident Response Protocol

### 10.1 Severity Levels

| Severity | Definition                                          | Example                              |
|----------|-----------------------------------------------------|--------------------------------------|
| SEV-1    | Production service completely down                  | API health endpoint returns 5xx      |
| SEV-2    | Critical path broken but service partially running  | Chat canary failing, UI probe broken |
| SEV-3    | Degraded performance or quality regression          | Response time 3x above baseline      |
| SEV-4    | Minor issue detected, no user impact                | Warning pattern in logs              |

### 10.2 Response Requirements

**SEV-1: Immediate Response**

- Response time: immediate (within minutes)
- Allowed action: bounded hotfix or rollback
- Hotfix must still produce: implementation result, abbreviated review,
  validation evidence
- Post-incident review required within 24 hours
- Full design review for permanent fix required within 72 hours

**SEV-2: Urgent Response**

- Response time: within 1 hour
- Action: create P0 task, generate design brief
- Abbreviated stage chain allowed (G1 lightweight + G3 + G4 abbreviated + G5 focused + G6)
- Full post-incident review required within 48 hours

**SEV-3: Scheduled Response**

- Response time: within 1 business day
- Action: create P1 task, full stage chain
- Standard lifecycle applies

**SEV-4: Tracked Response**

- Response time: next planning cycle
- Action: create P2/P3 task, full stage chain
- May be deferred by controller

### 10.3 Post-Incident Review

Every SEV-1 and SEV-2 incident must produce a post-incident review containing:

1. `timeline`: what happened and when
2. `root_cause`: why it happened
3. `detection_method`: how monitoring detected it (or why it did not)
4. `corrective_action`: what was done to fix it
5. `preventive_measures`: what will prevent recurrence
6. `monitoring_improvements`: new checks or thresholds to add
7. `test_additions`: new tests to catch this class of failure

The post-incident review re-enters the lifecycle as a Class B or Class C
change to implement the preventive measures.

## 11. Environment Separation

Production quality requires environment separation. At minimum, the project
must maintain:

1. **Development**: mutable, used for feature work and local debugging.
2. **Sandbox/Evolution**: isolated, used for delegated agent experimentation
   and self-evolution proposals. Cannot directly mutate production.
3. **Staging**: release-candidate-only, used for pre-production validation.
4. **Production**: stable, only reviewed and promoted behavior.

Separation must cover:

- code workspace
- process/port space
- database
- cache/queue
- vector/knowledge store
- runtime artifacts
- configuration

Direct sandbox-to-production promotion is prohibited.

## 12. Rollback Discipline

### 12.1 Recovery Layers

Three recovery layers must be maintained:

1. **Code rollback**: git branch, commit, and tag discipline. Every accepted
   milestone has a recoverable pointer.

2. **Config rollback**: configuration must be versioned. A release is not
   recoverable if code can be rolled back but config cannot.

3. **Data/runtime rollback**: database backups, cache state, vector store
   snapshots, and runtime artifacts must be preserved for risky promotions.

### 12.2 Rollback Triggers

Rollback should be considered immediately when:

- canonical runtime path fails after promotion
- truth surfaces become ambiguous
- memory or knowledge is unexpectedly polluted
- service launch path no longer matches reviewed baseline
- self-evolution changes controller behavior without approval

### 12.3 Rollback Execution

Rollback is a bounded packet:

1. identify target rollback version
2. restore code
3. restore config
4. restore data if needed
5. validate canonical service path
6. archive rollback result

## 13. Milestone and Archive Discipline

### 13.1 Checkpoint vs Milestone vs Archive Point

- **Checkpoint**: safety snapshot of work in progress. Not for formal
  acceptance.
- **Milestone**: bounded result with validation, review, and evidence.
  Eligible for formal acceptance and tagging.
- **Archive point**: milestone or larger boundary that should remain easy
  to recover, replay, or compare later.

### 13.2 Milestone Criteria

A result qualifies as a milestone when:

1. scope is bounded and named
2. result has a clear claim boundary
3. validation was run
4. result can be reviewed externally
5. review outcome is accepted or absorbed with explicit changes
6. result is committed to git
7. result can be traced through docs, tests, and handoff

### 13.3 Archive Rules

- every meaningful milestone must be committed
- every accepted milestone should be tagged
- every review result worth keeping must be written and committed
- every high-risk operation must have a rollback pointer
- every promotion must be tied to a known code + config state
