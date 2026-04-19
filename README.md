# AgentSDLC

A comprehensive lifecycle governance framework for AI agent-driven software development.

## What is this?

When AI coding agents (GPT, Claude, Gemini, Qwen, etc.) work on a real project, they face a fundamental quality problem: **changes accumulate fast, but review, testing, and governance do not keep up.**

AgentSDLC provides a complete operating contract that a **development controller agent** can follow to enforce quality across the full software development lifecycle — from design to production monitoring, and back again.

## The Problem It Solves

Without governance, AI coding projects tend to:

- Ship unreviewed code directly to production
- Let agents accept their own output as final
- Skip testing for "small" changes that turn out to break things
- Lose the ability to roll back when something goes wrong
- Accumulate monitoring debt until production issues become invisible

AgentSDLC prevents these failure modes by defining **7 stages, 7 gates, and 1 closed feedback loop**.

## The Lifecycle

```
Design → Design Review → Code Implementation → Code Review → Testing → Go-to-Production → Runtime Monitoring
  ↑                                                                                              |
  └──────────────────────────────────── Feedback Loop ───────────────────────────────────────────┘
```

Every change must pass through stage gates (G1–G7). No gate may be skipped. No agent may accept its own work.

## Document Structure

| File | Purpose |
|------|---------|
| [`AI_CODING_LIFECYCLE_GOVERNANCE.md`](AI_CODING_LIFECYCLE_GOVERNANCE.md) | **Main contract.** Full stage definitions, gate criteria, change classification, agent roles, delivery formats, incident response, environment separation, rollback discipline. |
| [`STAGE_GATES_REFERENCE.md`](STAGE_GATES_REFERENCE.md) | **Quick reference card.** At-a-glance gate entry/exit criteria, decision matrices, required test levels by change class. |
| [`MONITORING_FEEDBACK_LOOP.md`](MONITORING_FEEDBACK_LOOP.md) | **Closed-loop spec.** Signal classification, issue-to-task conversion, three task paths (auto/controller/incident), severity SLAs, post-incident review. |
| [`AGENT_ROLE_MATRIX.md`](AGENT_ROLE_MATRIX.md) | **Role mapping.** Stage participation matrix, decision authority, handoff protocols, escalation rules, pipeline depth limits. |

## Quick Start

### 1. Give the contract to your controller agent

Add the main governance document to your agent's system prompt or project-level instructions:

```
You are the development controller for this project.
Follow the operating contract in AI_CODING_LIFECYCLE_GOVERNANCE.md.
```

### 2. Define your agent roles

Use the role template in [`AGENT_ROLE_MATRIX.md` §7](AGENT_ROLE_MATRIX.md#7-agent-contract-template) to create contracts for each agent role in your project:

- **Controller** — coordinates lifecycle, makes gate decisions
- **Architect Agent** — design analysis, option comparison
- **Coding Agent** — bounded implementation
- **Code Review Agent** — patch review, regression detection
- **Test Fixer Agent** — test repair, validation
- **Refactor Agent** — structural cleanup

### 3. Classify your changes

Every change gets a class that determines the minimum gate requirements:

| Class | Scope | Example | Minimum Gates |
|-------|-------|---------|---------------|
| **A** | Safe local change | Bug fix, doc correction | G1 (lightweight) + G3 + G4 + G5 |
| **B** | Runtime behavior change | New API route, state machine change | All gates (G1–G6) |
| **C** | Environment / data / schema | DB migration, config change | All gates + enhanced validation |
| **D** | Self-evolution rule change | Agent boundary modification | All gates + maximum scrutiny |

### 4. Enforce the feedback loop

When monitoring detects an issue, it follows one of three paths back into design:

- **Path A (Auto):** Well-understood, bounded fix → auto-generate design brief → controller approves → standard lifecycle
- **Path B (Controller):** Requires judgment → controller triages → design brief or defer
- **Path C (Incident):** Production down → immediate hotfix → post-incident review within 24h → permanent fix enters lifecycle

### 5. Track governance health

Key metrics from the framework:

| Metric | Target | Red Flag |
|--------|--------|----------|
| Gate skip rate | 0% | Any skip |
| Self-acceptance rate | 0% | Any self-accept |
| Promotion without staging | 0% | Any direct-to-prod |
| Post-incident review rate | 100% (SEV-1/2) | Any missing |

## Core Principles

1. **Controller coordinates, does not code.** The controller writes briefs and makes gate decisions, not production code.
2. **No self-acceptance.** Every output is a candidate until reviewed by the controller.
3. **Bounded work.** One task, one scope, one result. If scope expands, stop and report.
4. **Evidence-based promotion.** Changes become promotable only after passing the full gate chain.
5. **Rollback is always possible.** If you can't go back, you're not under control.

## Adapting to Your Project

This framework is **project-agnostic**. It does not assume any specific:

- Programming language or framework
- CI/CD platform
- Hosting environment
- AI model provider
- Team size or structure

To adapt it:

1. Keep the stage definitions and gate criteria as-is (they are the governance backbone).
2. Customize the monitoring signal sources table for your infrastructure.
3. Adjust check intervals and SLAs to match your project's scale.
4. Add project-specific forbidden shortcuts if needed.
5. Define concrete validation commands for each gate in your project context.

## License

MIT
