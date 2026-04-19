# AgentSDLC Instantiation Guide

> How to apply this governance framework to a real project.

---

## Overview

AgentSDLC is project-agnostic. To use it, you **instantiate** it for your
specific project by:

1. Filling in `PROJECT_PROFILE.md` with your project's real values
2. Adopting or merging `agents.md` into your project's agent operating contract
3. Verifying alignment between your existing rules and the framework

---

## Prerequisites

Before starting, ensure you have access to:

- The AgentSDLC source files (this repository)
- Your project's codebase and configuration
- Knowledge of your project's tech stack, services, environments, and health checks

---

## Step-by-Step Process

### Step 1: Classify Your Project

Determine your project's baseline classification:

| Dimension | Options | Criteria |
|-----------|---------|----------|
| Level | L1 / L2 / L3 | L1: scripts/tools. L2: persistent project with infra. L3: multi-team/production-critical |
| Type | A / B / C / D | A: one-off. B: persistent iterative. C: infra/platform. D: self-evolution |
| Risk | R1 / R2 / R3 | R1: low blast radius. R2: moderate. R3: high/irreversible |
| Change Class | A / B / C / D | A: cosmetic. B: behavior within boundary. C: interface/contract. D: authority/self-modification |

Use these to determine your **default gate profile**:
- L1 + R1/R2 → G-Lite (T1)
- L2 + R2, L1 + R3 → G-Std (T2)
- L2 + R3, L3 + any → G-Full (T3)

### Step 2: Fill PROJECT_PROFILE.md

Copy `PROJECT_PROFILE.md` from this repository into your project root.
Fill in every section:

| Section | Required? | What to Fill |
|---------|-----------|-------------|
| 1. Project Identity | Yes | Name, repo, branch, language, framework, classification, gate profile |
| 2. Environment Matrix | Yes | All environments with purpose, URL/host, deploy method |
| 3. Canonical User Paths | Recommended | Critical user-facing flows that define "system is working" |
| 4. Critical Connectors | Recommended | Databases, APIs, external services with timeouts and fallbacks |
| 5. Validation Commands | Yes | How to verify the project builds, passes tests, starts correctly |
| 6. Health Endpoints | Recommended | Endpoints used for runtime monitoring |
| 7. Rollback Assets | Yes (T2+) | How to undo each type of change |
| 8. Alert Channels | Recommended | How incidents are communicated by severity |
| 9. Project-Specific Overrides | If needed | Any deviations from default rules with justification |
| 10. Notes | Optional | Temporary workarounds, known constraints |

**Rules:**
- If a field is unknown, write `TODO` — do not leave it blank or fake it.
- If your project doesn't have something (e.g., staging env), document the gap explicitly.
- Use your project's actual values, not aspirational ones.

### Step 3: Adopt or Merge agents.md

Your project may already have an agent operating contract (e.g., `AGENTS.md`).

**If no existing contract exists:**
- Copy `agents.md` from this repository into your project root
- Customize the classification defaults for your project

**If an existing contract exists:**
- Read both files
- Identify what is **already aligned** (no action needed)
- Identify what is **missing** from your existing contract
- Merge missing items without breaking existing project-specific rules
- **Never overwrite** — existing contracts may have project-specific rules that
  are not in the generic framework

**Key items your contract should include** (from `agents.md`):
- Classification rules (Level + Type + Risk + Change Class)
- Non-negotiable rules (controller not coder, no self-acceptance, stop and report, etc.)
- Gate profile awareness (all 7 gates, profile determines strictness)
- Class D human sign-off rule
- Artifact chain requirements
- Completion discipline (no vague "done" language)

### Step 4: Verify Environment Separation

Check your environment matrix against the framework requirements:

| Project Level | Minimum Environments |
|---------------|---------------------|
| L1 | dev + production |
| L2 | dev + staging + production |
| L3 / Class D work | dev + sandbox + staging + production |

If your project doesn't meet the minimum, document it as a gap with a plan
to close it.

### Step 5: Verify Monitoring Coverage

Ensure your project has:
- [ ] Health endpoints for each running service
- [ ] A monitoring mechanism (watchdog, cron, external monitor)
- [ ] Defined check intervals
- [ ] Clear severity definitions (SEV-1 / SEV-2 / SEV-3)
- [ ] At least one alert channel

If any are missing, document them as TODOs in the profile.

### Step 6: Validate

After completing the above, verify:

- [ ] `PROJECT_PROFILE.md` exists at project root with all required sections
- [ ] All gaps are documented as explicit TODOs
- [ ] Your agent contract includes the key governance items
- [ ] Environment separation meets or documents the minimum
- [ ] Monitoring coverage is documented
- [ ] No existing rules were broken or removed

---

## Delegation Format

When delegating this instantiation task to an agent, provide:

1. **The facts** — project name, tech stack, services, ports, health endpoints,
   connectors, environment details, validation commands
2. **Source files to read** — paths to the template and reference contract
3. **Constraints** — no code changes, no behavior changes, preserve existing rules
4. **Acceptance criteria** — checklist of what "done" looks like
5. **Classification** — the task's own governance classification (typically Class B, G-Std)

---

## Common Patterns

### Pattern: Single developer, single agent

Most common setup. Use defaults:
- Single controller with role-switching
- G-Std as default profile
- Class D requires stopping and reporting (human sign-off)

### Pattern: Personal project with watchdog

If your project has a runtime watchdog or health monitor:
- Reference it in the "monitoring source" field
- Map its check interval to the health endpoint table
- Ensure watchdog can trigger alert channels

### Pattern: Existing agent contract

If your project already has strong agent rules:
- Don't duplicate — reference the framework for items not yet covered
- Add `PROJECT_PROFILE.md` for the operational parameters
- Add classification + gate profile awareness to the existing contract

---

## After Instantiation

Once your project is instantiated:

1. **Use the gate profile** for every change — classify, then follow the profile
2. **Fill artifact chain fields** on deliverables — task_id, change_id, etc.
3. **Document gaps as you discover them** — update PROJECT_PROFILE.md
4. **Review after real friction** — if gates are too heavy or too light, adjust
   the classification, not the framework
