# Project Model Routing Policy

## 1. Scope

This document is **project-level runtime policy**, not universal SDLC.  
It defines how the main agent must route tasks to models and workers during architecture, implementation, review, testing, and high-risk governance.

---

## 2. Core Rule

Use **strong but scarce models only for high-value decisions**, not for routine throughput.

Default routing principle:

- **Qwen3.6-Plus** = daily architecture + frontend + long-context synthesis
- **GLM-5** = backend implementation + routine testing
- **Kimi K2.5** = primary reviewer
- **MiniMax-M2.5** = high-speed implementation / bulk refactor / bulk test generation
- **GLM-5.1** = high-risk arbitration, final architecture judgment, final gate
- **Claude worker** = optional independent second-opinion reviewer for high-value cases

---

## 3. Model Roles

### Qwen3.6-Plus
Use for:
- architecture first draft
- OpenClaw / Hermes / workflow structure design
- large document digestion
- frontend / UI / GUI / page / component work
- tasks requiring very long context or multimodal understanding

Do not use alone for final judgment on Class C / D or high-risk architecture.

### GLM-5
Use for:
- backend services
- APIs
- business logic
- bug fixes
- ordinary refactors
- unit / integration / regression tests
- implementation-heavy work

### Kimi K2.5
Use for:
- design review
- code review
- test review
- finding missing scenarios
- finding weak assumptions
- checking for over-design or scope drift

Kimi is the default independent reviewer.

### MiniMax-M2.5
Use for:
- first draft implementation
- repetitive code changes
- boilerplate generation
- bulk refactor
- bulk test filling
- high-volume low-complexity tasks

### GLM-5.1
Use only for:
- memory / state / scheduler / orchestration
- core architecture disputes
- persistent data / schema / critical backend path
- security / permission / deletion / irreversible actions
- R3 tasks
- Class C / D tasks
- disagreement between other models
- pre-release final review for high-risk changes
- post-incident permanent fix design
- high-risk validation strategy

Do not use GLM-5.1 for routine throughput.

### Claude Worker
Enable only when:
- a high-risk task needs an additional independent review
- the main agent has low confidence in current conclusions
- architecture conclusions are disputed
- an incident fix needs extra external review
- Class C / D changes need stronger independence

Claude worker is not a default pipeline step.

---

## 4. Routing by Task Type

### Architecture
- Daily architecture: **Qwen3.6-Plus** + **Kimi K2.5**
- High-risk architecture: Qwen3.6-Plus draft -> Kimi review -> optional Claude worker -> **GLM-5.1 final**

### Backend Implementation
- Routine backend: **GLM-5** + **Kimi K2.5**
- Repetitive/high-throughput backend: **MiniMax-M2.5** + **Kimi K2.5**
- High-risk backend: **GLM-5** + **Kimi K2.5** + **GLM-5.1 final**

### Frontend Implementation
- Routine frontend: **Qwen3.6-Plus** + **Kimi K2.5**
- Bulk UI generation: **MiniMax-M2.5** + **Kimi K2.5**
- Long PRD / screenshots / design specs: **Qwen3.6-Plus**

### Review
- Default review: **Kimi K2.5**
- Long-context consistency review: **Qwen3.6-Plus**
- High-risk review: **GLM-5.1**
- Independent external review: optional **Claude worker**

### Testing
- Routine tests: **GLM-5**
- Bulk test generation: **MiniMax-M2.5**
- Frontend / GUI / document-heavy testing: **Qwen3.6-Plus**
- Test review / missing-case review: **Kimi K2.5**
- High-risk validation strategy: **GLM-5.1**

---

## 5. Escalation Rules

The main agent must escalate to **GLM-5.1** if any of the following is true:

- task is R3
- task is Class C or D
- task affects memory / state / scheduler / orchestration
- task affects persistence / schema / critical data paths
- task affects security / permission / deletion
- architecture conclusions conflict across models
- release requires high-risk final gate
- incident permanent fix is being designed

The main agent must enable **Kimi K2.5 review** unless the task is trivial and low-risk.

The main agent may enable **Claude worker** for additional independence, but must not make Claude worker a mandatory default reviewer.

---

## 6. Standard Execution Patterns

### Routine Feature Flow
1. Qwen3.6-Plus creates architecture or requirement interpretation
2. GLM-5 implements backend or Qwen3.6-Plus implements frontend
3. MiniMax-M2.5 handles bulk repetitive work if needed
4. Kimi K2.5 reviews
5. Escalate to GLM-5.1 only if risk or disagreement requires it

### High-Risk Architecture Flow
1. Qwen3.6-Plus drafts architecture
2. Kimi K2.5 reviews
3. Claude worker optionally provides second opinion
4. GLM-5.1 makes final decision
5. GLM-5 implements
6. Kimi K2.5 reviews implementation
7. GLM-5.1 handles final high-risk gate

### Incident Recovery Flow
1. GLM-5 or MiniMax-M2.5 implements temporary fix
2. Kimi K2.5 checks whether fix is superficial
3. Qwen3.6-Plus synthesizes incident context
4. GLM-5.1 decides permanent fix strategy
5. GLM-5 implements permanent fix
6. Kimi K2.5 reviews
7. GLM-5.1 handles final high-risk validation if needed

---

## 7. Prohibitions

The main agent must not:

- use GLM-5.1 as the default routine model
- allow the implementation model to be the only reviewer
- let MiniMax-M2.5 independently decide high-risk architecture
- let Qwen3.6-Plus alone approve Class C / D changes
- use Claude worker as the default reviewer for all tasks
- skip independent review on high-risk changes
- ignore conflicts between model conclusions

---

## 8. Required Routing Record

For every non-trivial task, the main agent must record:

```text
Task Type:
Risk Level:
Change Class:
Primary Model:
Review Model:
Claude Worker:
Final Arbiter:
Reason:
```

---

## 9. Compressed Rule

**Use Qwen3.6-Plus for daily architecture and frontend, GLM-5 for backend and routine testing, Kimi K2.5 for primary review, MiniMax-M2.5 for high-speed bulk work, GLM-5.1 for high-risk final judgment, and Claude worker only for optional independent second-opinion review.**
