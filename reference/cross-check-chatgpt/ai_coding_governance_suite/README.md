# AI Coding Governance Suite (Lightweight Layered Edition)

This package is a practical governance kit for a development control agent in small or personal AI coding projects.

## Intended use
- Single-developer or small-team projects
- Long-running personal systems
- AI agent / workflow / API / automation projects
- Lightweight by default, but able to upgrade for higher risk work

## Package structure
- `docs/governance/AGENT_GOVERNANCE_SUITE.md` — master governance spec
- `docs/governance/PROJECT_CLASSIFICATION.md` — project/risk/template classification rules
- `docs/governance/REVIEW_CHECKLISTS.md` — stage-by-stage checklists
- `docs/governance/AI_PROJECT_GUARDRAILS.md` — AI-specific controls
- `docs/governance/INCIDENT_CLOSED_LOOP.md` — runtime issue to improvement loop
- `.agent/agents.md` — drop-in control-agent operating prompt
- `templates/` — lightweight cards for structured execution
- `runbooks/` — release, rollback, incident handling runbooks

## Default recommendation
For most personal, long-running AI projects:
- Default level: `L2`
- Default type: `B` / `C` / `D` based on actual task
- Default risk: `R2`
- Default governance template: `T2`
- Auto-upgrade to `R3/T3` when touching memory, state, orchestration, persistence, core loop, deletion/permission, external connectors, or production incident fixes

## Suggested adoption path
1. Start by using `.agent/agents.md`
2. Keep `REVIEW_CHECKLISTS.md` next to active work
3. Use `AI_PROJECT_GUARDRAILS.md` for agent/workflow systems
4. Use `INCIDENT_CLOSED_LOOP.md` after every runtime issue
5. Gradually adopt templates and runbooks as the project becomes more persistent

## Notes
This suite is intentionally not enterprise-heavy. It focuses on:
- right-sized process
- explicit classification
- evidence-based completion
- rollback readiness
- continuous closed-loop improvement
