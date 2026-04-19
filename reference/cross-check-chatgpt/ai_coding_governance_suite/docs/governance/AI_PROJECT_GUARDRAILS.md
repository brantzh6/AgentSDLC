# AI_PROJECT_GUARDRAILS.md

## 1. Purpose

This document adds AI-specific controls for projects involving:
- agents
- tool calling
- workflows
- memory
- stateful planning
- orchestration
- mixed deterministic/model-driven behavior

These controls apply in addition to the baseline governance suite.

---

## 2. Why AI projects need special rules

AI coding systems often fail in ways that normal software checklists do not fully cover:
- false confidence
- fake completion claims
- context drift
- unbounded retries
- prompt-driven behavior drift
- memory contamination
- silent state corruption
- logic hidden inside prompts or assumptions

Because of this, AI-related work must be treated with extra discipline even in small projects.

---

## 3. High-frequency AI project risks

The control agent must watch closely for:

1. **Fake completion**  
   Claiming success without implementation or verification.

2. **Context drift**  
   The implementation no longer matches the original task or approved plan.

3. **Inference/logic mixing**  
   Deterministic logic and model output assumptions are treated as the same thing.

4. **Tool invocation drift**  
   Tools are called in contexts that were not intended.

5. **Main-loop instability**  
   The agent can retry, recurse, or branch without strong stop control.

6. **Memory contamination**  
   The system stores stale, wrong, or speculative content as if it were truth.

7. **State transition gaps**  
   The workflow enters undefined states or cannot exit cleanly.

8. **Runtime-only patching**  
   Incidents are treated as isolated bugs rather than system-learning inputs.

---

## 4. Mandatory rules

### Rule 1 — No fake completion
The control agent must reject statements like:
- should be fixed
- probably works
- logically complete
- likely safe
- should pass tests

Use instead:
- implemented
- validated
- not validated
- pending manual confirmation
- risk remains

### Rule 2 — Unknowns must be explicit
If something was not actually checked, run, or verified, mark it clearly as:
- unknown
- unverified
- inferred
- needs confirmation

### Rule 3 — Separate deterministic logic from model behavior
For every meaningful AI feature, clarify:
- what is deterministic system logic
- what is model inference
- what is prompt behavior
- what comes from tool results
- what depends on runtime context

### Rule 4 — Memory/state/orchestration changes are high risk
Any task touching:
- memory
- persistent state
- state machine
- task orchestration
- scheduler
- core agent loop

must be treated as `R3` unless there is a strong reason not to.

### Rule 5 — Every agent loop needs exit or degradation behavior
The system must not rely on endless retries or implicit hope.
For important loops, define:
- retry conditions
- stop conditions
- fallback path
- user-visible failure mode or safe exit

### Rule 6 — Prompts are not a substitute for control logic
Prompting can guide behavior, but critical controls must not depend only on prompt wording.
Critical constraints should be reinforced by:
- code logic
- validators
- tool restrictions
- state checks
- explicit branching rules

### Rule 7 — Incidents must become controls
After an AI-related issue, the control agent must convert the lesson into one or more durable assets:
- a checklist item
- a test case
- a rule
- a guardrail
- a runbook update
- a monitoring point

---

## 5. AI-specific design questions

For Type C work, the control agent should ask:

### Context
- What context is required?
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
- Is there a downgrade path?

---

## 6. AI-specific validation guidance

For AI workflow changes, baseline validation should include:
- expected context path
- missing context path
- tool success path
- tool failure path
- malformed input path
- state exit path

For higher-risk changes, include:
- loop stability checks
- repeated failure behavior
- stale memory behavior
- incorrect tool result behavior
- interruption / recovery behavior

---

## 7. Red flags

The control agent should slow down and escalate scrutiny when it sees:
- "let the model decide everything"
- hidden routing rules inside prompts only
- memory written without review gates
- retries without stop criteria
- broad fallbacks masking failures
- agent behavior not tied to explicit state
- system claims based on inference instead of evidence

---

## 8. Success criteria

AI governance is working when:
- the system is less likely to fake progress
- agent behavior stays closer to intended boundaries
- runtime issues are easier to explain and reproduce
- memory/state logic becomes more deliberate over time
- the project improves from incidents instead of repeatedly improvising around them
