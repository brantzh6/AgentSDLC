# AI Coding Governance Suite v2 (Lightweight Layered Edition)

This package is a practical governance kit for a development control agent in small or personal AI coding projects.

Version 2 keeps the original lightweight layered model, and adds sharper control points inspired by a stronger lifecycle contract:
- change classes `A/B/C/D`
- controller-default-is-coordinator, not default primary coder
- explicit `no self-acceptance`
- `stop and report` when blocked instead of guessing
- structured monitoring-to-task conversion
- incident hotfix path that must return to the normal lifecycle
- pipeline depth limits to avoid controller overload

## Intended use
- single-developer or small-team projects
- long-running personal systems
- AI agent / workflow / API / automation projects
- lightweight by default, but able to upgrade for higher-risk work

## Package structure
- `docs/governance/AGENT_GOVERNANCE_SUITE.md` — master governance spec
- `docs/governance/PROJECT_CLASSIFICATION.md` — project/risk/template/change-class rules
- `docs/governance/REVIEW_CHECKLISTS.md` — stage-by-stage checklists
- `docs/governance/AI_PROJECT_GUARDRAILS.md` — AI-specific controls
- `docs/governance/INCIDENT_CLOSED_LOOP.md` — runtime issue to improvement loop
- `docs/governance/DELIVERY_TEMPLATES.md` — T1/T2/T3 delivery expectations
- `docs/governance/AGENTSDLC_INSIGHTS_ADOPTED.md` — v2 deltas absorbed from an external AgentSDLC-style contract
- `.agent/agents.md` — drop-in control-agent operating prompt
- `templates/` — lightweight cards for structured execution
- `runbooks/` — release, rollback, incident handling runbooks

## Default recommendation
For most personal, long-running AI projects:
- default level: `L2`
- default type: `B` / `C` / `D` based on actual task
- default risk: `R2`
- default change class: `B` unless clearly `A`, `C`, or `D`
- default governance template: `T2`
- auto-upgrade to `R3/T3` when touching memory, state, orchestration, persistence, core loop, deletion/permission, external connectors, incident repair, agent boundary rules, or auto-modification permissions

## Suggested adoption path
1. Start by using `.agent/agents.md`
2. Keep `PROJECT_CLASSIFICATION.md` and `REVIEW_CHECKLISTS.md` next to active work
3. Use `AI_PROJECT_GUARDRAILS.md` for agent/workflow systems
4. Use `INCIDENT_CLOSED_LOOP.md` after every runtime issue
5. Use `AGENTSDLC_INSIGHTS_ADOPTED.md` to understand the v2 control upgrades
6. Gradually adopt templates and runbooks as the project becomes more persistent

## Notes
This suite is intentionally not enterprise-heavy. It focuses on:
- right-sized process
- explicit classification
- evidence-based completion
- rollback readiness
- continuous closed-loop improvement
- stronger controller discipline without turning every project into a heavy SDLC machine
