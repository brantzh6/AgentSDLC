# AI-Specific Guardrails

This document defines controls for AI coding projects that go beyond standard
software governance. These risks are unique to systems involving agents, tool
calling, workflows, memory, stateful planning, orchestration, and mixed
deterministic/model-driven behavior.

For full lifecycle context, see `AI_CODING_LIFECYCLE_GOVERNANCE.md`.

## 1. Why AI Projects Need Special Rules

AI coding systems fail in ways that normal checklists do not cover:

- **False confidence**: agents claim completion without evidence
- **Context drift**: implementation diverges from the approved plan
- **Inference/logic mixing**: model output treated as deterministic fact
- **Tool invocation drift**: tools called in unintended contexts
- **Memory contamination**: stale or speculative content stored as truth
- **Silent state corruption**: workflows enter undefined states
- **Unbounded retries**: agents loop without stop conditions
- **Prompt-as-logic**: critical constraints live only in prompt text

Because of this, AI-related work must be treated with extra discipline,
even in small personal projects.

## 2. Mandatory Rules

### Rule 1: No Fake Completion

The controller must reject vague completion claims:

Banned:
- "should be fixed"
- "probably works"
- "logically complete"
- "likely safe"
- "should pass tests"

Required:
- "implemented" (code is written)
- "validated" (evidence exists)
- "not validated" (not yet checked)
- "pending manual confirmation"
- "risk remains"

### Rule 2: Unknowns Must Be Explicit

If something was not actually checked, run, or verified, mark it clearly:

- `unknown` — no information available
- `unverified` — not yet checked
- `inferred` — based on reasoning, not evidence
- `needs confirmation` — requires manual action

### Rule 3: Separate Deterministic Logic from Model Behavior

For every meaningful AI feature, clarify:

- What is **deterministic system logic** (code that always does the same thing)
- What is **model inference** (output that may vary)
- What is **prompt behavior** (guided by prompt wording)
- What comes from **tool results** (external data)
- What depends on **runtime context** (may change between runs)

### Rule 4: Memory/State/Orchestration Changes Are High Risk

Any task touching these areas must be treated as R3 (high risk) unless
there is a strong, documented reason not to:

- memory or long-term state
- persistent state or state machine
- task orchestration or scheduler
- core agent loop or control loop

### Rule 5: Every Loop Needs Exit Behavior

The system must not rely on endless retries or implicit hope. For every
important loop, define:

- **retry conditions**: when to try again
- **stop conditions**: when to stop trying
- **fallback path**: what to do when stopped
- **user-visible failure mode**: how failure is surfaced (not silently swallowed)

### Rule 6: Prompts Are Not Control Logic

Prompting can guide behavior, but critical controls must not depend only on
prompt wording. Critical constraints should be reinforced by:

- code logic
- validators
- tool restrictions
- state checks
- explicit branching rules

If the only thing preventing a dangerous action is a line in a prompt,
it is not under control.

### Rule 7: Incidents Must Become Controls

After an AI-related issue, the controller must convert the lesson into at
least one durable asset:

- a checklist item
- a test case
- a governance rule
- a guardrail
- a runbook update
- a monitoring point
- a design constraint

"Fixed in code only" is not sufficient closure.

### Rule 8: Class D Requires Explicit Governance Sign-Off

Changes that affect what the system can modify about itself (Class D) must
have a written governance sign-off that addresses:

- what authority, boundary, or mutation scope is being changed
- why the change is justified
- what the rollback path is
- what monitoring will verify the change is safe

Class D changes:
- always force T3 and R3
- may not land directly to production
- may not self-accept
- may not silently delegate approval authority
- require rollback drill or equivalent confidence validation

### Rule 9: Environment Boundaries for Agent Projects

Agent, memory, and orchestration projects (Type C) must respect environment
boundaries. Many dangerous failures in these systems are not code bugs but
"boundary drift" — the agent starts modifying surfaces it should not touch,
or self-evolution proposals bypass normal review.

For Type C projects:
- sandbox/evolution environment is recommended even at L2
- boundary changes (Class D) must be tested in sandbox before staging
- memory contract changes must be validated in isolation before merging

## 3. High-Frequency AI Risk Catalog

The controller should actively watch for these patterns:

| # | Risk | Description | Indicator |
|---|------|-------------|-----------|
| 1 | Fake completion | Claiming success without implementation or verification | Vague language, no evidence |
| 2 | Context drift | Implementation no longer matches the approved plan | Scope creep, plan/code mismatch |
| 3 | Inference/logic mixing | Model output treated as deterministic fact | No separation of logic layers |
| 4 | Tool invocation drift | Tools called in contexts not intended | Unexpected tool calls in logs |
| 5 | Main-loop instability | Agent retries/recurses without stop control | Unbounded loops, no exit path |
| 6 | Memory contamination | Stale/wrong/speculative content stored as truth | Memory written without review |
| 7 | State transition gaps | Workflow enters undefined states or cannot exit | Stuck states, orphan tasks |
| 8 | Runtime-only patching | Issues treated as isolated bugs, not system inputs | No durable improvement after fix |
| 9 | Boundary drift | Agent modifies surfaces it should not touch | Unexpected mutations, permission creep |
| 10 | Authority escalation | Self-evolution proposals bypass review | Auto-modification without sign-off |

## 4. AI-Specific Design Questions

When working on AI agent/workflow features (Type C projects), the controller
should ask these questions during design:

### Context
- What context is required for this feature?
- What context is optional?
- What happens if context is missing or stale?

### Tools
- Under what conditions may each tool be called?
- What tool results are trusted directly?
- What happens if the tool returns incomplete or conflicting results?

### State
- What are the valid states?
- How does the workflow enter each state?
- How does it exit each state?
- Is there any ambiguous or sticky state?

### Memory
- What can be stored?
- What cannot be stored?
- How is stale information prevented from becoming truth?
- Is memory advisory, operational, or authoritative?

### Failure
- How does the system stop bad loops?
- What happens after repeated failures?
- Is there a downgrade/degradation path?

## 5. AI-Specific Validation Paths

For AI workflow changes, validation should include:

### Baseline (all tiers)
- expected context path (context present, correct)
- missing context path (context absent or stale)
- tool success path (tool returns expected result)
- tool failure path (tool fails or returns error)
- malformed input path (unexpected input format)
- state exit path (workflow can terminate cleanly)

### Higher risk (T2/T3)
- loop stability check (does the loop terminate?)
- repeated failure behavior (what happens after N failures?)
- stale memory behavior (what happens with outdated memory?)
- incorrect tool result behavior (what if tool returns wrong data?)
- interruption/recovery behavior (what if the process is killed mid-way?)

## 6. Red Flags

The controller should slow down and increase scrutiny when it sees:

- "let the model decide everything"
- hidden routing rules inside prompts only
- memory written without review gates
- retries without stop criteria
- broad fallbacks masking actual failures
- agent behavior not tied to explicit state
- system claims based on inference instead of evidence
- "it works on my machine" without reproducible evidence

## 7. Success Criteria

AI guardrails are working when:

- the system is less likely to fake progress
- agent behavior stays closer to intended boundaries
- runtime issues are easier to explain and reproduce
- memory/state logic becomes more deliberate over time
- the project improves from incidents instead of repeatedly improvising
