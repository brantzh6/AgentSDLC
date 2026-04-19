# AI Coding Lifecycle Governance Contract

Status: v3.1
Version: 3.1

## 1. Purpose and Scope

This document is the operating contract for any development controller agent
managing an AI-driven coding project.

It governs:

- how changes move from idea to production
- what gates must be passed at each stage
- who holds decision authority
- how governance intensity scales with project size and risk
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
   - In single-agent mode, this means explicit role-switching between
     "implementer" and "reviewer" perspectives.

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
   - A change becomes promotable only after passing the required gate chain.

7. Rollback must always be possible.
   - If the project cannot return to a previously trusted state, it is not
     under control.

8. Design before implementation.
   - Do not jump into coding for any non-trivial task.
   - Every task must have at least a minimal design statement appropriate
     for its governance tier.

9. Pipeline discipline.
   - No more than 3 tasks in active implementation/review/validation.
   - No more than 1 task in active promotion.
   - Do not start new major work when the pipeline is overloaded,
     unless handling an incident.

### 2.2 Operating Defaults

- UTF-8 encoding for all project files.
- Minimal safe patches preferred over large rewrites.
- No dependencies added without explicit approval.
- No architecture decisions made by delegate agents.
- No unrelated fixes mixed into one change unit.

### 2.3 Right-Sized Governance

The goal is **disciplined evolution, not maximum process**.

- Use the lightest governance that still preserves correctness, verification,
  rollback ability, and runtime learning.
- Do not use enterprise-heavy process for trivial one-off work.
- Do not treat persistent projects with a demo mindset.
- Governance intensity must scale with project size, project type, and
  change risk (see Section 3).

## 3. Project Classification and Governance Tiering

Before starting any task, the controller must classify the work along three
dimensions and select the appropriate governance template.

### 3.1 Project Level

| Level | Name                   | Examples                                          |
|-------|------------------------|---------------------------------------------------|
| L1    | One-off / low-persist  | Temporary scripts, demos, one-time data jobs      |
| L2    | Persistent personal    | Personal API, long-running agent, private system   |
| L3    | Multi-user / high-loss | External service, shared system, deletion-capable  |

Default assumption if unclear: **L2**.

### 3.2 Project Type

| Type | Name                       | Main Concerns                                         |
|------|----------------------------|-------------------------------------------------------|
| A    | Script / automation        | Input/output correctness, idempotency, retry safety   |
| B    | App / API / website        | Main-path usability, compatibility, error handling     |
| C    | AI agent / workflow system | Context correctness, state closure, fake completion    |
| D    | Integration / data flow    | Protocol stability, timeout/retry, duplicate handling  |

### 3.3 Change Risk

| Risk | Name        | Examples                                                      |
|------|-------------|---------------------------------------------------------------|
| R1   | Low risk    | Docs, comments, tiny UI tweaks, non-behavioral cleanup        |
| R2   | Medium risk | Standard feature, local logic change, prompt tuning           |
| R3   | High risk   | State/memory logic, schema migration, core loop, permissions  |

Default assumption if unclear: **R2**.

**Automatic escalation to R3** when a task touches:

- memory or long-term state
- state machine or state transition rules
- task orchestration or scheduler logic
- persistence layout or migration
- deletion paths or permission logic
- core external connectors
- main agent loop or control loop
- production incident remediation

### 3.4 Change Class

Change class is **separate from risk**. Risk describes how dangerous this
specific modification is. Class describes **what kind of system surface is
being changed**, and therefore what minimum governance chain must apply.

| Class | Name                                | Typical Cases                                            |
|-------|-------------------------------------|----------------------------------------------------------|
| A     | Safe local change                   | Small bug fix, docs correction, narrow test addition     |
| B     | Runtime behavior change             | New route, API change, workflow branch, state machine     |
| C     | Environment / data / schema / memory| DB migration, memory contract, config, knowledge wiring   |
| D     | Self-evolution / boundary / authority| Auto-modification rules, review bypass, mutation perms    |

**Change-class override rules:**

- Class A: keep the base template unless other signals force higher.
- Class B: minimum template is **T2** for persistent projects.
- Class C: minimum template is **T2**; for L2/L3, default to **T3**.
- Class D: **always force T3 and R3**. No self-acceptance. No direct
  production landing. No silent delegation of approval authority.
  Requires **explicit governance sign-off** (see Section 7.3).

Default assumption if unclear: **Class B**.

### 3.5 Governance Template Selection

| Template | Name        | When to Use                            | Gate Profile |
|----------|-------------|----------------------------------------|-------------|
| T1       | Lightweight | L1 + R1/R2, or L2 + R1                | G-Lite      |
| T2       | Standard    | L2 + R2, L1 + R3, most persistent work | G-Std       |
| T3       | Reinforced  | L2 + R3, L3 + any, blast-radius high  | G-Full      |

**All 7 gates (G1-G7) always exist.** The gate profile determines the
**strictness and acceptance form** of each gate, not whether it exists.
A G-Lite gate is still passed — it is just passed in a lighter form
(self-check instead of full review, focused check instead of full test suite).

**Gate profiles** bridge the gap between flexible tiering and structured gates:

- **G-Lite**: all 7 gates in lightweight form — self-check reviews,
  focused validation, simple deployment, basic monitoring.
  Suitable for bounded, easily reversible work.
- **G-Std**: standard gate chain — structured design, role-switch review,
  implement, code review, standard test, staged deploy.
  Suitable for most persistent project work.
- **G-Full**: all 7 gates in full rigor — dedicated reviews,
  full test suites, full promotion chain, full monitoring.
  Required for high-risk, Class C/D, or production-critical changes.

After selecting a base template from `Level + Risk`, apply the change-class
override rules from Section 3.4 to raise the template if needed.

Default assumption if unclear: **T2**.

### 3.6 Task Classification Output

Before starting a task, the controller should produce:

```
## Task Classification
- Project Level: L1 / L2 / L3
- Project Type: A / B / C / D
- Change Risk: R1 / R2 / R3
- Change Class: A / B / C / D
- Governance Template: T1 / T2 / T3
- Gate Profile: G-Lite / G-Std / G-Full

## Why
- <concise reasons for the classification>
- <note any class override applied>
```

## 4. Lifecycle Stage Definitions

The lifecycle consists of seven stages forming a continuous loop:

```
Design -> Design Review -> Code Implementation -> Code Review -> Testing -> Go-to-Production -> Runtime Monitoring
  ^                                                                                                    |
  |____________________________________________________________________________________________________|
                                          (Feedback Loop)
```

All 7 gates apply to every task. The gate profile determines the
strictness and acceptance form of each gate (see Section 3.4).

### Stage 1: Design

**Tier applicability: T1 (lightweight brief), T2 (standard brief), T3 (full brief with alternatives)**

Purpose: Define what will be built, why, and within what boundaries.

#### T1 Minimum Design

- goal
- non-goals
- main risk
- validation approach
- rollback note

#### T2 Standard Design

- problem statement
- proposed approach
- scope boundary (in-scope, out-of-scope)
- affected files and surfaces
- constraints and assumptions
- success criteria
- stop conditions
- change class

#### T3 Full Design

All T2 fields plus:
- current state analysis
- at least two alternative approaches compared
- trade-offs explicit per approach
- risks identified per approach
- monitoring strategy
- failure handling strategy
- recommended path with justification

Entry criteria:

- a clear problem statement or monitoring-generated task exists
- the change is scoped to a bounded unit

Exit criteria (Gate G1: Design Complete):

- design brief exists with all fields required for the selected tier
- change class is determined
- scope boundary is explicit

Responsible role: controller (with architect perspective)

### Stage 2: Design Review

**Tier applicability: T1 (self-review), T2 (role-switching review), T3 (independent review)**

Purpose: Validate the design before any implementation begins.

#### T1 Self-Review

The controller performs a quick self-check:
- Is the goal understood correctly?
- Is there a simpler approach?
- Are there hidden assumptions?
- Is this over-designed for the task size?

#### T2 Role-Switching Review

The controller switches to a reviewer perspective and checks:
- Is the plan aligned with the actual goal?
- Is complexity appropriate?
- Are failure paths included?
- Is it testable and monitorable?
- Is the release/rollback realistic?

Produce a review result: `approve`, `conditional_approve`, `reject`.

#### T3 Full Design Review

Same as T2 plus:
- Are state transitions closed?
- Are consistency risks addressed?
- Are dependency failures handled?
- Does the plan add uncontrolled operational burden?

Entry criteria:

- design brief passes Gate G1

Exit criteria (Gate G2: Design Approved):

- review result exists (even if self-review for T1)
- controller has explicitly approved

Responsible role: controller (reviewer perspective)

### Stage 3: Code Implementation

**Tier applicability: All tiers (T1/T2/T3)**

Purpose: Produce the bounded code change described in the approved design.

Required activities (all tiers):

1. Read the approved design brief and context files.
2. Make the smallest safe patch that satisfies the brief.
3. Run the validation commands specified in the brief.
4. Produce a structured implementation result containing:
   - summary
   - files changed
   - validation run output
   - known risks
   - recommendation

Safe working pattern:

- stay inside the brief
- do not broaden scope
- do not start new architecture branches
- do not mix unrelated fixes
- do not change backend semantics in UI-only tasks
- if blocked, stop and report instead of guessing

Entry criteria:

- design brief has passed Gate G2 (or G1 self-review for T1)

Exit criteria (Gate G3: Implementation Complete):

- implementation result exists with all required sections populated
- validation commands from the brief have been executed
- no placeholder sections remain

Responsible role: controller (implementer perspective)

### Stage 4: Code Review

**Tier applicability: T1 (skip or self-check), T2 (role-switching review), T3 (full review)**

Purpose: Verify the implementation for correctness, regressions, and scope
discipline before testing.

#### T1 Minimal Check

Quick self-check:
- Does the code match the plan?
- Any obvious bugs?
- Did scope silently expand?
- Are errors swallowed silently?

#### T2 Role-Switching Review

The controller switches to reviewer perspective:
- behavioral regressions
- incorrect assumptions about data, API, or state
- scope creep beyond the brief
- missing validation
- security or stability concerns

Produce a review result: `accept`, `accept_with_changes`, `reject`.

#### T3 Full Review

Same as T2 plus:
- Is the state machine closed?
- Are retries/timeouts/fallbacks safe?
- Are consistency assumptions explicit?
- Are external dependency failures handled?
- Are monitoring hooks sufficient?
- Is rollback genuinely executable?

Entry criteria:

- implementation result passes Gate G3

Exit criteria (Gate G4: Code Review Passed):

- review result exists
- all blockers resolved

Responsible role: controller (reviewer perspective)

### Stage 5: Testing

**Tier applicability: T1 (focused check), T2 (standard testing), T3 (full testing)**

Purpose: Verify the change works correctly and does not regress existing
behavior.

#### T1 Focused Check

- main path manually exercised or spot-checked
- 1-3 important scenarios verified
- at least 1 failure scenario checked

#### T2 Standard Testing

- existing test suite run (no regressions)
- focused tests for the changed behavior
- at least 1 regression check
- at least 1 failure path

#### T3 Full Testing

All T2 plus:
- integration tests
- smoke tests against live-like environment
- migration validation (if applicable)
- rollback feasibility check (Class C+)
- rollback drill (Class D)

Test requirements by change class (for T2/T3):

- Class A: compile check + focused unit tests
- Class B: Class A + integration tests + smoke tests
- Class C: Class B + migration validation + rollback feasibility check
- Class D: Class C + explicit rollback drill

Produce a test result containing:

- test suite: pass/fail with counts
- new tests added (list)
- regression surface covered
- known gaps
- recommendation: `pass`, `pass_with_gaps`, `fail`

Entry criteria:

- implementation passes Gate G4 (or G3 for T1)

Exit criteria (Gate G5: Testing Passed):

- test result exists
- all required test levels for the tier and change class executed
- no critical failures remain

Responsible role: controller (tester perspective)

### Stage 6: Go-to-Production

**Tier applicability: T1 (simple deploy + verify), T2 (staged deploy), T3 (full promotion chain)**

Purpose: Promote the validated change safely.

#### T1 Simple Deploy

- keep old version available
- deploy the change
- run main path after deploy
- know rollback path

#### T2 Staged Deploy

- pre-promotion checklist (scope frozen, review done, validation done,
  rollback pointer exists)
- deploy to staging/test environment
- validate in staging
- promote to production
- post-promotion verification

#### T3 Full Promotion Chain

All T2 plus:
- staging validation with health check, canonical paths, regression surface
- explicit observation window after production deploy
- explicit stop conditions defined before promotion
- rollback path confirmed valid at every step

Promotion order (T2/T3):

1. development -> staging
2. staging -> production

Direct development-to-production is prohibited for T2/T3.
T1 may deploy directly if the project has no staging environment.

Entry criteria:

- change passes Gate G5

Exit criteria (Gate G6: Production Deployed):

- deployment completed
- post-deployment verification passed
- rollback path confirmed valid

Responsible role: controller

### Stage 7: Runtime Monitoring

**Tier applicability: T1 (basic health), T2 (standard monitoring), T3 (full monitoring)**

Purpose: Continuously verify production health and detect issues.

#### T1 Basic Health

At minimum, be able to answer:
- Is it alive?
- Are failures increasing?
- Is the main task succeeding?

#### T2 Standard Monitoring

- health endpoint checks (every few minutes)
- log pattern analysis (periodic)
- self-test loops (smoke tests against live services)
- basic daily reports

#### T3 Full Monitoring

All T2 plus:
- minute-level health checks with canary tests
- deep log analysis (every 15-60 min)
- scheduled evaluation (daily/weekly quality assessment)
- performance regression detection
- detailed daily reports with insights

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
  (see Section 5)

Responsible role: monitoring system (automated) or controller (manual for T1)

## 5. The Closed Loop: Monitoring to Design

This is the critical feedback mechanism that makes the lifecycle continuous.

### 5.1 Signal Classification

Monitoring signals are classified into four levels:

| Level    | Meaning                                        | Response Time |
|----------|------------------------------------------------|---------------|
| Critical | Production service down or critical path broken | Immediate     |
| Warning  | Degraded behavior or recurring errors detected  | Within 1 hour |
| Degraded | Quality metrics below threshold                 | Within 1 day  |
| Info     | Trend observation, no immediate action needed   | Next cycle    |

### 5.2 Issue-to-Task Conversion

When monitoring detects an actionable finding:

1. The finding is converted to a structured task containing:
   - source: monitoring system identifier
   - source_type: job / route / connector / loop / memory / scheduler / alert
   - source_id: unique signal identifier (for dedup and traceability)
   - signal_level: critical / warning / degraded / info
   - evidence: log entries, health snapshots, metric values
   - affected_surface: which component or path is impacted
   - auto_processible: whether automated correction is safe
   - category: reliability / performance / quality / security

2. Duplicate detection: if an open task for the same source and category
   already exists, update it instead of creating a new one.

3. Priority mapping:
   - critical -> P0
   - warning -> P1
   - degraded -> P2
   - info -> P3

### 5.3 Task-to-Design-Brief Conversion

Tasks that require code changes re-enter the lifecycle at Stage 1:

1. For auto-processible tasks (bounded, well-understood, safe):
   - generate a minimal design brief automatically
   - the brief must still pass review (controller approval)
   - proceed through the gate chain appropriate for the governance tier

2. For controller-escalated tasks:
   - controller reviews the monitoring task
   - controller decides: ignore (with reason), defer, or create design brief
   - if design brief created, proceed through the standard gate chain

3. For incident-level findings (critical):
   - immediate bounded corrective action is allowed (hotfix path)
   - hotfix still requires: implementation result, abbreviated review,
     validation, and post-deployment verification
   - a full post-incident design review must follow within 24 hours

### 5.4 Feedback Loop Rules

1. No monitoring finding may directly modify production code without
   passing through at least an abbreviated stage chain.

2. Auto-evolution or self-modification proposals must go through the
   full stage chain. They may not self-accept.

3. Every corrective action triggered by monitoring must be archived
   with traceability back to the originating monitoring signal.

4. Post-incident reviews must produce:
   - root cause analysis
   - corrective action taken
   - preventive measures
   - at least one durable improvement (test, monitoring rule, checklist
     update, design restriction, or runbook update)

## 6. Stage Gate Summary

### 6.1 Gate Profiles — What Each Gate Requires

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

### 6.2 Gate Reference

| Gate | Name                  | Key Requirement                           | Decision By  |
|------|-----------------------|-------------------------------------------|--------------| 
| G1   | Design Complete       | Brief with scope, constraints, criteria   | Controller   |
| G2   | Design Approved       | Review passed, controller approved        | Controller   |
| G3   | Implementation Done   | Result with validation, no placeholders   | Controller   |
| G4   | Code Review Passed    | Review findings addressed, accepted       | Controller   |
| G5   | Testing Passed        | Required test levels executed, no fails   | Controller   |
| G6   | Production Deployed   | Staging + prod validation passed          | Controller   |
| G7   | Monitoring Active     | Continuous; triggers loop on findings     | Automated    |

## 7. Change Classification and Class Controls

Change class definitions are in Section 3.4. This section defines the
control requirements per class.

### 7.1 Class-Specific Minimum Controls

| Control                     | Class A | Class B | Class C         | Class D           |
|-----------------------------|---------|---------|-----------------|-------------------|
| Minimum template            | T1      | T2*     | T2 (T3 for L2+) | T3 (forced)       |
| Minimum risk                | --      | --      | --              | R3 (forced)       |
| Rollback note               | Yes     | Yes     | Explicit plan   | Explicit plan     |
| Compatibility check         | --      | --      | Required        | Required          |
| Migration/backup readiness  | --      | --      | Required        | Required          |
| Direct production landing   | Allowed | Allowed | Cautious        | **Prohibited**    |
| Governance sign-off         | --      | --      | Controller      | Explicit (7.3)    |

*Class B minimum T2 only for persistent (L2+) projects.

### 7.2 Class-Aware Validation

- Class A: focused validation
- Class B: focused + integration/smoke as needed
- Class C: Class B + compatibility/migration/rollback readiness
- Class D: Class C + authority/boundary impact review + rollback drill

### 7.3 Class D Governance Sign-Off

Class D changes affect what the system can modify about itself. These require:

1. **Human sign-off required by default.** In single-agent mode, the controller
   may prepare a sign-off draft, but the final sign-off must come from a human
   operator. Without human sign-off, the change must stay in sandbox/staging
   and may not enter production. This rule may only be relaxed when a project
   has an explicit, documented exemption with compensating controls.

2. **Explicit governance sign-off** — not implicit pass. The sign-off must
   address:
   - what authority/boundary/mutation scope is being changed
   - what the rollback path is
   - why the change is justified
   - what monitoring will verify the change is safe

3. **No silent delegation** — the sign-off cannot be delegated to the same
   agent that implemented the change.

4. **No direct production landing** — Class D must go through staging/sandbox
   first.

5. **Rollback drill or equivalent** — for meaningful Class D changes, a
   rollback drill or equivalent confidence-building validation is required.

## 8. Agent Operating Model

### 8.1 Single-Agent Mode (Default)

The default operating model is a **single controller agent** that switches
between roles as needed:

1. **Designer**: analyze the problem, draft the design brief
2. **Reviewer**: switch perspective, critique the design
3. **Implementer**: write the bounded code change
4. **Code Reviewer**: switch perspective, review the implementation
5. **Tester**: validate the change
6. **Operator**: deploy, monitor, handle feedback loop

Key discipline for single-agent role-switching:

- When switching to reviewer role, explicitly challenge the implementer's
  assumptions. Do not rubber-stamp your own work.
- When reviewing, ask: "If someone else wrote this, what would I question?"
- Mark clearly when you are switching roles in your output.

### 8.2 Multi-Agent Mode (Scale-Up Path)

When multi-agent coordination becomes available, the controller can delegate
to specialized agents:

- **Architect Agent**: design analysis, option comparison
- **Coding Agent**: bounded implementation
- **Code Review Agent**: patch review, regression detection
- **Test Fixer Agent**: test repair, validation
- **Refactor Agent**: structural cleanup

In multi-agent mode:

- delegates recommend, only the controller decides
- no delegate may accept its own output
- no delegate may bypass the controller's gate decision
- see `AGENT_ROLE_MATRIX.md` for full handoff protocols

### 8.3 Role Boundaries (Both Modes)

The controller may not:
- delegate its own gate decision authority
- auto-accept its own outputs without explicit role-switching review
- bypass stage gates
- silently broaden scope

## 9. Delivery Format Requirements

### 9.0 Artifact Chain Schema (All Artifacts)

Every artifact (brief, result, review, test, promotion) must carry the
following **unified artifact fields**. This is the minimum schema that
enables audit trail reconstruction.

| Field              | Type     | Description                                           |
|--------------------|----------|-------------------------------------------------------|
| `task_id`          | Required | Unique task identifier (e.g., `T-2026-0419-001`)     |
| `change_id`        | Required | Change unit reference (links related artifacts)        |
| `change_class`     | Required | A / B / C / D at time of artifact creation             |
| `gate_profile`     | Required | G-Lite / G-Std / G-Full                                |
| `parent_artifact`  | Required | Reference to preceding artifact in the chain           |
| `decision`         | Required | Gate outcome (approve / reject / conditional / blocked)|
| `decision_by`      | Required | Who made the gate decision (role or identity)          |
| `decided_at`       | Required | When the decision was made                             |
| `evidence`         | T2+      | Pointer to validation output or test result            |
| `known_risks`      | T2+      | Residual risks carried forward                         |
| `rollback_pointer` | T2+      | How to undo this artifact's effect                     |

**Audit trail rule**: from any promotion record, it must be possible to
trace back through the full artifact chain to the original design brief.
If any link in the chain is missing, the promotion record is incomplete.

### 9.1 Design Brief

Required fields (adjust depth by tier):

1. `task_id` (all tiers)
2. `change_class` (all tiers)
3. `problem_statement` (T1: goal)
4. `proposed_approach` (T1: plan)
5. `scope_boundary` (T1: non-goals)
6. `affected_files` (T2+)
7. `constraints` (T2+)
8. `success_criteria` (T2+)
9. `stop_conditions` (T2+)

### 9.2 Implementation Result

Required fields (all tiers):

1. `summary`
2. `files_changed`
3. `validation_run`
4. `known_risks`
5. `recommendation` (accept / accept_with_changes / reject / blocked)

### 9.3 Review Result

Required fields (T2+):

1. `findings` (ordered by severity)
2. `open_questions`
3. `recommendation` (approve / approve_with_changes / reject)

### 9.4 Test Result

Required fields (adjust depth by tier):

1. `test_suite_result` (pass / fail with counts)
2. `new_tests_added`
3. `known_gaps`
4. `recommendation` (pass / pass_with_gaps / fail)

### 9.5 Promotion Record (T2+)

1. `task_id`
2. `change_id`
3. `parent_artifact` (link to test result)
4. `scope_summary`
5. `build_version`
6. `promoted_from` (e.g., staging)
7. `promoted_to` (e.g., production)
8. `pre_promotion_evidence`
9. `validation_evidence`
10. `git_checkpoint`
11. `rollback_pointer`
12. `monitoring_baseline` (what signals to watch post-promotion)
13. `post_promotion_verification`
14. `decision_by`

### 9.6 Blocked Report (Any Stage)

1. `blocker` (what prevents completion)
2. `what_was_attempted`
3. `what_is_missing`
4. `recommendation` (escalate / defer / abort)

## 10. Forbidden Shortcuts

1. **Chat-only acceptance**: no change is accepted just because it was discussed.
2. **Self-acceptance without role-switch**: review must use a different perspective.
3. **Direct experimental patch to production**: T2/T3 must go through staging.
4. **Mixed promotion units**: unrelated fixes must not be bundled.
5. **Self-evolution auto-promotion**: auto-modification must go through full review.
6. **Gate completeness**: all 7 gates apply to every change. The gate
   profile determines the strictness of each gate, not whether it applies.
7. **Silent scope broadening**: if scope expands, stop and report.
8. **Validation-free acceptance**: missing validation = accept_with_changes or reject.
9. **Undocumented hotfixes**: even emergency fixes must produce a result.
10. **Rollback-free promotion**: if rollback is not possible, the change is not promotable.

## 11. Incident Response Protocol

### 11.1 Severity Levels

| Severity | Definition                                          |
|----------|-----------------------------------------------------|
| SEV-1    | Production service completely down                  |
| SEV-2    | Critical path broken but service partially running  |
| SEV-3    | Degraded performance or quality regression          |
| SEV-4    | Minor issue detected, no user impact                |

### 11.2 Response Requirements

**SEV-1**: Immediate response, bounded hotfix or rollback allowed.
Post-incident review required within 24 hours.

**SEV-2**: Response within 1 hour, abbreviated stage chain allowed.
Post-incident review required within 48 hours.

**SEV-3**: Response within 1 business day, standard lifecycle.

**SEV-4**: Next planning cycle, may be deferred by controller.

### 11.3 Post-Incident Review

Every SEV-1 and SEV-2 must produce:

1. `timeline`: what happened and when
2. `root_cause`: why it happened
3. `detection_method`: how monitoring detected it
4. `corrective_action`: what was done
5. `preventive_measures`: what will prevent recurrence
6. `durable_improvement`: at least one of: new test, monitoring rule,
   checklist update, design restriction, runbook update

A runtime issue is not closed when the service works again. It is closed
only when at least one durable anti-recurrence improvement is in place.

## 12. Language Discipline

### 12.1 Banned Phrases

The controller must reject vague completion language:

- "should work"
- "probably fixed"
- "likely complete"
- "theoretically safe"
- "should pass tests"
- "logically correct"

### 12.2 Required Phrases

Use explicit status language:

- "implemented" (code is written)
- "validated" (tests/checks have been run with evidence)
- "unverified" (not yet checked)
- "needs manual confirmation" (requires human action)
- "remaining risk exists" (known gap)
- "inferred" (based on reasoning, not evidence)
- "unknown" (no information available)

Anything not directly checked, run, or validated must be marked as
`unknown`, `inferred`, `unverified`, or `pending confirmation`.

## 13. AI-Specific Guardrails

AI coding projects have failure modes that normal software checklists do not
cover. See `AI_GUARDRAILS.md` for the complete set of AI-specific controls.

Key rules summarized here:

1. **No fake completion**: reject claims without evidence.
2. **Separate inference from logic**: clarify what is deterministic code
   vs model output vs prompt behavior vs tool results.
3. **Memory/state changes are high risk**: always R3 unless justified.
4. **Every loop needs exit behavior**: no endless retries or implicit hope.
5. **Prompts are not control logic**: critical constraints must be
   reinforced by code, validators, or state checks.
6. **Incidents must become controls**: every issue produces at least one
   durable improvement.

## 14. Environment Separation

Production quality requires environment separation. The required zones
scale with project level and governance tier.

### 14.1 Environment Matrix

| Environment         | L1 / T1           | L2 / T2            | L3 / T3 / Class D    |
|---------------------|--------------------|---------------------|-----------------------|
| Development         | Required           | Required            | Required              |
| Runtime (local)     | Often same as dev  | Recommended         | Required              |
| Sandbox / Evolution | --                 | Optional            | Required              |
| Staging             | --                 | Recommended         | Required              |
| Production          | Often same as dev  | Required            | Required              |

### 14.2 Promotion Paths

- **L1/T1**: dev -> production (direct allowed)
- **L2/T2**: dev -> staging -> production (direct-to-prod prohibited)
- **L3/T3/Class D**: dev -> sandbox -> staging -> production
  (sandbox-to-prod prohibited, staging must validate first)

### 14.3 Environment Rules

1. **Development**: mutable, used for feature work and local debugging.
2. **Sandbox/Evolution**: isolated experimentation. Especially important for
   agent/memory/orchestration projects where boundary changes and self-evolution
   proposals need safe testing before reaching production.
3. **Staging**: release-candidate validation before production.
4. **Production**: stable, only reviewed and promoted behavior.

## 15. Rollback Discipline

### 15.1 Recovery Layers

1. **Code rollback**: git branch, commit, and tag discipline.
2. **Config rollback**: configuration must be versioned.
3. **Data/runtime rollback** (T3/Class C+): database backups, cache state,
   runtime artifacts preserved for risky promotions.

### 15.2 Rollback Triggers

Rollback should be considered when:

- canonical runtime path fails after promotion
- truth surfaces become ambiguous
- memory or knowledge is unexpectedly polluted
- service launch path no longer matches reviewed baseline

### 15.3 Minimum Rollback by Tier

- T1: keep old version, know how to restore it
- T2: git checkpoint + config versioning + explicit rollback method
- T3: full three-layer recovery (code + config + data)

## 16. Milestone and Archive Discipline

- **Checkpoint**: safety snapshot of work in progress.
- **Milestone**: bounded result with validation, review, and evidence.
- **Archive point**: milestone that should remain easy to recover.

Milestone criteria:

1. scope is bounded and named
2. validation was run
3. result can be reviewed
4. result is committed to git
5. for T2+: result is tagged

## 17. Companion Documents

| Document                        | Purpose                                              |
|---------------------------------|------------------------------------------------------|
| `STAGE_GATES_REFERENCE.md`      | Quick-reference card for all 7 gates and gate profiles |
| `MONITORING_FEEDBACK_LOOP.md`   | Closed-loop specification                            |
| `AGENT_ROLE_MATRIX.md`          | Role mapping: single-agent + multi-agent reference   |
| `AI_GUARDRAILS.md`              | AI-specific controls and guardrails                  |
| `CHECKLISTS.md`                 | Stage-by-stage checklists per governance tier        |
| `TEMPLATES.md`                  | Fill-in-the-blank task, design, incident templates   |
| `RUNBOOKS.md`                   | Step-by-step release, rollback, incident runbooks    |
| `agents.md`                     | Drop-in agent operating prompt for any project       |
| `PROJECT_PROFILE.md`            | Per-project instantiation template                   |
| `INSTANTIATION_GUIDE.md`        | How to apply this framework to a real project        |
