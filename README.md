# AgentSDLC

**A lifecycle governance harness for AI agent-driven software development.**

AgentSDLC provides the rules, gates, templates, and operating prompts that
controller agents (Hermes, OpenClaw, or any LLM-based development orchestrator)
need to govern the full software development lifecycle — from design through
production monitoring with a closed feedback loop.

---

## What This Is

This is not a framework to discuss. It is a **harness to execute**.

An agent that adopts AgentSDLC gets:

- A **classification system** to right-size governance per task
- A **7-gate lifecycle** where every gate always applies, with strictness
  determined by profile (G-Lite / G-Std / G-Full)
- **Non-negotiable rules**: controller is coordinator not coder, no
  self-acceptance, stop and report, evidence-based promotion, rollback
  must always be possible
- **Templates and checklists** for structured delivery
- **A closed feedback loop** from runtime monitoring back to design
- **A drop-in operating prompt** (`agents.md`) ready to paste into any
  controller agent

---

## Quick Start

### 1. Classify your project

| Dimension    | Options              |
|-------------|----------------------|
| Level       | L1 / L2 / L3        |
| Type        | A / B / C / D        |
| Risk        | R1 / R2 / R3        |
| Change Class| A / B / C / D        |

### 2. Pick the gate profile

| Tier | Gate Profile | When |
|------|-------------|------|
| T1   | G-Lite      | One-off scripts, low-risk fixes |
| T2   | G-Std       | Persistent projects, normal work |
| T3   | G-Full      | High-risk, state/memory/core-loop changes |

All 7 gates (G1-G7) always exist. The gate profile determines the **strictness**
of each gate, not whether it applies.

### 3. Load the operating prompt

Copy [`agents.md`](agents.md) into your project's agent configuration directory.
It contains everything a controller agent needs to operate under this harness.

### 4. Instantiate for your project

Follow the [`instantiation/GUIDE.md`](instantiation/GUIDE.md) and fill in
[`instantiation/PROJECT_PROFILE.md`](instantiation/PROJECT_PROFILE.md) with
your project's real values.

---

## Repository Structure

```
AgentSDLC/
├── README.md                       ← you are here
├── agents.md                       ← drop-in controller operating prompt
│
├── governance/                     ← rules and contracts
│   ├── LIFECYCLE_CONTRACT.md       ← main contract (stages, gates, classification)
│   ├── STAGE_GATES.md              ← gate-by-gate detail per profile
│   ├── GUARDRAILS.md               ← AI-specific safety controls
│   ├── AGENT_ROLES.md              ← role definitions and switching model
│   └── MONITORING_FEEDBACK.md      ← closed-loop signal handling
│
├── operations/                     ← day-to-day tooling
│   ├── TEMPLATES.md                ← task, result, incident templates
│   ├── CHECKLISTS.md               ← stage-by-stage verification
│   └── RUNBOOKS.md                 ← release, rollback, incident procedures
│
├── instantiation/                  ← how to adopt
│   ├── GUIDE.md                    ← step-by-step setup process
│   └── PROJECT_PROFILE.md          ← per-project template to fill
│
└── reference/                      ← samples and cross-references
    ├── model-routing-policy-pack/  ← sample: task→model routing rules
    └── cross-check-chatgpt/        ← ChatGPT v1/v2 governance comparison
```

---

## Document Map

### For Agents (load these)

| Document | What It Does |
|----------|-------------|
| [`agents.md`](agents.md) | **Load this first.** Complete operating prompt with classification, rules, gate awareness, completion discipline. |

### Governance (the rule system)

| Document | What It Does |
|----------|-------------|
| [`governance/LIFECYCLE_CONTRACT.md`](governance/LIFECYCLE_CONTRACT.md) | Main contract. 7 stages, 7 gates, 4-dimensional classification, gate profiles, artifact chain schema, environment matrix, promotion discipline. |
| [`governance/STAGE_GATES.md`](governance/STAGE_GATES.md) | Gate-by-gate detail. Entry/exit criteria per profile (G-Lite/G-Std/G-Full). |
| [`governance/GUARDRAILS.md`](governance/GUARDRAILS.md) | AI-specific controls: Class D sign-off, boundary drift, authority escalation, truth-surface integrity. |
| [`governance/AGENT_ROLES.md`](governance/AGENT_ROLES.md) | Single-agent role-switching (default) + multi-agent delegation reference. |
| [`governance/MONITORING_FEEDBACK.md`](governance/MONITORING_FEEDBACK.md) | Signal schema, issue-to-task conversion, 3 handling paths, severity SLAs, health-based closure. |

### Operations (use these daily)

| Document | What It Does |
|----------|-------------|
| [`operations/TEMPLATES.md`](operations/TEMPLATES.md) | Fill-in templates for T1/T2/T3 tasks, results, incidents, promotion records, controller exceptions. |
| [`operations/CHECKLISTS.md`](operations/CHECKLISTS.md) | Per-stage verification checklists including classification, Class D, and environment checks. |
| [`operations/RUNBOOKS.md`](operations/RUNBOOKS.md) | Step-by-step procedures for release, rollback, incident handling, recurrence escalation. |

### Instantiation (adopt for your project)

| Document | What It Does |
|----------|-------------|
| [`instantiation/GUIDE.md`](instantiation/GUIDE.md) | Step-by-step process: classify → fill profile → adopt prompt → verify. |
| [`instantiation/PROJECT_PROFILE.md`](instantiation/PROJECT_PROFILE.md) | Template: environment matrix, connectors, health endpoints, validation commands, rollback assets. |

### Reference (samples)

| Document | What It Does |
|----------|-------------|
| [`reference/model-routing-policy-pack/`](reference/model-routing-policy-pack/) | **Sample:** Project-level task→model routing rules. Shows how to map task types to specific models with escalation criteria. |
| [`reference/cross-check-chatgpt/`](reference/cross-check-chatgpt/) | Cross-analysis between this framework and ChatGPT's independent governance design. |

---

## Design Principles

1. **Right-sized governance** — T1 tasks get lightweight gates, not full ceremony
2. **7 gates always exist** — profile controls strictness, not existence
3. **Controller is coordinator, not coder** — delegates implement, controller governs
4. **No self-acceptance** — review must use a different perspective
5. **Evidence over assertion** — validation backed by output, not vague claims
6. **Rollback must always be possible** — every promotion answers "how to undo"
7. **Pipeline discipline** — max 3 active implementation, max 1 active promotion
8. **Class D requires human sign-off** — self-evolution changes need human approval
9. **Closed feedback loop** — runtime signals create new classified tasks, not hot edits

---

## Version

v3.1 — 7 stages, 7 gates, 3 gate profiles, 4 change classes, closed feedback loop.
