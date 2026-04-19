# AgentSDLC

A comprehensive lifecycle governance framework for AI agent-driven software development.

**Version 3** — with change classes, gate profiles, artifact chains, and aligned cross-check improvements.

## What is this?

When AI coding agents work on a real project, they face a fundamental quality problem: **changes accumulate fast, but review, testing, and governance do not keep up.**

AgentSDLC provides a complete operating contract that a **development controller agent** can follow to enforce quality across the full software development lifecycle — from design to production monitoring, and back again.

The framework is **right-sized by default**: it scales from lightweight single-agent personal projects to reinforced multi-agent team workflows.

## The Problem It Solves

Without governance, AI coding projects tend to:

- Ship unreviewed code directly to production
- Let agents accept their own output as final
- Skip testing for "small" changes that turn out to break things
- Lose the ability to roll back when something goes wrong
- Accumulate monitoring debt until production issues become invisible
- Fake completion with vague language ("should work", "probably fixed")

AgentSDLC prevents these failure modes with **7 stages, 7 gates, 3 gate profiles, 4 change classes, governance tiering, and 1 closed feedback loop**.

## Key Design Principle

> **The goal is disciplined evolution, not maximum process.**

Use the lightest governance that still preserves correctness, verification, rollback ability, and runtime learning.

## Governance Tiering

Not every task needs the full ceremony. AgentSDLC uses three tiers:

| Tier | Name        | Gate Profile | When to Use                                      |
|------|-------------|-------------|--------------------------------------------------|
| T1   | Lightweight | G-Lite      | One-off scripts, low-risk fixes, small personal tasks |
| T2   | Standard    | G-Std       | Persistent personal projects, most normal work   |
| T3   | Reinforced  | G-Full      | High-risk changes, state/memory/core-loop work   |

All 7 gates (G1-G7) always exist. The gate profile determines the **strictness** of each gate, not whether it applies.

The tier is selected by classifying the project and task across **four dimensions**:

- **Project Level**: L1 (one-off) / L2 (persistent personal) / L3 (multi-user)
- **Project Type**: A (script) / B (app/API) / C (AI agent/workflow) / D (integration)
- **Change Risk**: R1 (low) / R2 (medium) / R3 (high)
- **Change Class**: A (safe local) / B (runtime behavior) / C (env/data/memory) / D (self-evolution/authority)

Change class can **override the minimum governance template** — a small R1 docs fix is Class A,
but a change to self-evolution permissions is Class D, forcing T3 regardless of project size.

## The Lifecycle

```
Design -> Design Review -> Code Implementation -> Code Review -> Testing -> Go-to-Production -> Runtime Monitoring
  ^                                                                                                    |
  |____________________________________________________________________________________________________|
                                          (Feedback Loop)
```

Every change passes through all 7 gates. The gate profile determines the strictness of each gate. No agent may accept its own work without explicit role-switching review.

## Single-Agent vs Multi-Agent

**Default: single controller agent with role-switching.**

Most personal AI projects are operated by a single agent that switches between designer, reviewer, implementer, tester, and operator perspectives. This is the baseline assumption.

Multi-agent delegation (separate architect, coding, review, test agents) is described as a **scale-up path** for when cross-agent coordination becomes technically practical.

## Document Structure

| File | Purpose |
|------|---------|
| [`AI_CODING_LIFECYCLE_GOVERNANCE.md`](AI_CODING_LIFECYCLE_GOVERNANCE.md) | **Main contract.** Stage definitions, gate criteria, tiering, change classification, gate profiles, agent model, delivery formats, incident response, environment separation, rollback discipline. |
| [`agents.md`](agents.md) | **Drop-in agent prompt.** Copy-paste-ready operating prompt for any project's controller agent. |
| [`PROJECT_PROFILE.md`](PROJECT_PROFILE.md) | **Instantiation template.** Per-project profile with environment matrix, connectors, validation commands, health endpoints. |
| [`INSTANTIATION_GUIDE.md`](INSTANTIATION_GUIDE.md) | **Setup guide.** Step-by-step process for applying AgentSDLC to any project. |
| [`STAGE_GATES_REFERENCE.md`](STAGE_GATES_REFERENCE.md) | **Quick reference card.** Gate profiles (G-Lite/G-Std/G-Full), entry/exit criteria per tier, decision matrices. |
| [`MONITORING_FEEDBACK_LOOP.md`](MONITORING_FEEDBACK_LOOP.md) | **Closed-loop spec.** Signal schema, issue-to-task conversion, three handling paths, severity SLAs, health-based closure. |
| [`AGENT_ROLE_MATRIX.md`](AGENT_ROLE_MATRIX.md) | **Role mapping.** Single-agent role-switching guide + multi-agent scale-up reference. |
| [`AI_GUARDRAILS.md`](AI_GUARDRAILS.md) | **AI-specific controls.** Fake completion prevention, context drift, memory contamination, Class D sign-off, environment boundaries. |
| [`CHECKLISTS.md`](CHECKLISTS.md) | **Stage checklists.** Classification, design, review, validation, release, monitoring, closed-loop, language control. |
| [`TEMPLATES.md`](TEMPLATES.md) | **Fill-in-the-blank templates.** Task cards with artifact chain IDs, promotion records, incident closure, controller exception. |
| [`RUNBOOKS.md`](RUNBOOKS.md) | **Step-by-step guides.** Release (with pipeline check), rollback, incident handling (with recurrence escalation). |

## Quick Start

### 1. Give the contract to your controller agent

The fastest way: copy `agents.md` into your project's agent prompt directory.

Or use the full contract:
```
You are the development controller for this project.
Follow the operating contract in AI_CODING_LIFECYCLE_GOVERNANCE.md.
```

### 2. Classify every task before starting

```md
## Task Classification
- Task ID: T-2026-0419-001
- Project Level: L2
- Project Type: C
- Change Risk: R2
- Change Class: B
- Governance Template: T2
- Gate Profile: G-Std

## Why
- Persistent personal project
- AI agent/workflow system
- Standard feature work, runtime behavior change
```

### 3. Pass all 7 gates at the tier-appropriate strictness

- **T1**: Brief design -> implement -> focused test -> deploy
- **T2**: Design -> role-switch review -> implement -> code review -> test -> staged deploy
- **T3**: Full design with alternatives -> full review -> implement -> full review -> full test -> full promotion chain -> full monitoring

### 4. Use the checklists and templates

Copy the appropriate template from `TEMPLATES.md` and use the matching checklist from `CHECKLISTS.md` at each stage.

### 5. Enforce the feedback loop

When monitoring detects an issue, it follows one of three paths:

- **Path A (Auto)**: Bounded fix -> controller approves brief -> standard lifecycle
- **Path B (Controller)**: Controller triages -> design brief or defer
- **Path C (Incident)**: Immediate hotfix -> post-incident review within 24h

### 6. Track governance health

| Metric | Target | Red Flag |
|--------|--------|----------|
| Gate skip rate | 0% | Any skip |
| Self-acceptance without role-switch | 0% | Any rubber-stamp |
| Promotion without staging (T2+) | 0% | Any direct-to-prod |
| Durable improvement per incident | 1+ | Zero improvements |

## Core Principles

1. **Disciplined evolution, not maximum process.** Right-size governance to the task.
2. **No self-acceptance.** Review must use a different perspective, even in single-agent mode.
3. **Bounded work.** One task, one scope, one result. If scope expands, stop and report.
4. **Evidence over assertion.** Claims must be backed by validation, not vague language.
5. **Rollback is always possible.** If you cannot go back, you are not under control.
6. **Incidents become improvements.** Every runtime issue produces at least one durable control.
7. **Pipeline discipline.** Max 3 active tasks, max 1 promotion. Don't start new work when overloaded.

## Adapting to Your Project

This framework is **project-agnostic**. It does not assume any specific:

- Programming language or framework
- CI/CD platform
- Hosting environment
- AI model provider
- Team size or structure

To adapt it:

1. Start with T1 or T2 based on your project level and risk.
2. Use the task classification block before every non-trivial task.
3. Customize monitoring signal sources for your infrastructure.
4. Adjust check intervals and SLAs to your project's scale.
5. Gradually adopt templates and runbooks as the project grows.

## License

MIT
