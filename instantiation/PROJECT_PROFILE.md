# Project Profile

> This file is a per-project instantiation of the AgentSDLC framework.
> Copy this template into your project and fill in each section.
> Fields marked **[required]** must be set before using the framework;
> fields marked **[recommended]** are strongly encouraged for T2+ projects.

---

## 1. Project Identity

| Field                | Value                       |
|----------------------|-----------------------------|
| Project Name         | _your project name_         |
| Repository           | _repo URL or path_          |
| Default Branch       | `main`                      |
| Primary Language     | _e.g., Python 3.11, TypeScript 5_ |
| Framework            | _e.g., Next.js 14, FastAPI_ |
| Baseline Classification | _L? + Type ? + R? + Class ?_ |
| Default Gate Profile | _G-Lite / G-Std / G-Full_   |

---

## 2. Environment Matrix **[required]**

| Environment | Purpose               | URL / Host              | Deploy Method      |
|-------------|----------------------|-------------------------|--------------------|
| dev         | Local development    | `localhost:xxxx`        | manual             |
| sandbox     | Isolated experiments | _if applicable_         | _manual / script_  |
| staging     | Pre-production       | _staging URL_           | _CI / script_      |
| production  | Live                 | _production URL_        | _CI / gated_       |

> L1 projects: dev + production minimum.
> L2 projects: dev + staging + production minimum.
> L3 / Class D projects: dev + sandbox + staging + production required.

### Environment Version Map **[recommended for T2+]**

Track which release tag is currently deployed to each environment.
Update this table after every promotion to keep environment state auditable.

| Environment | Current Release Tag              | Previous Release Tag             | Last Promoted |
|-------------|----------------------------------|----------------------------------|---------------|
| dev         | _e.g., `release/2026-04-20-002`_ | _e.g., `release/2026-04-20-001`_ | _date_        |
| staging     | _e.g., `release/2026-04-19-003`_ | _e.g., `release/2026-04-19-002`_ | _date_        |
| production  | _e.g., `release/2026-04-18-001`_ | _e.g., `release/2026-04-17-002`_ | _date_        |

> This table enables environment-level auditing: at any point you can tell
> what version each environment is running and what to roll back to.

---

## 3. Canonical E2E Paths **[required for T2+]**

List the critical user-facing paths that define "the system is working."
These paths serve dual duty:
- **Monitoring**: health checks verify these paths to detect regressions
- **L3 Testing**: every T2+ test result must verify at least one of these
  paths end-to-end (see `governance/LIFECYCLE_CONTRACT.md` Section 5.2).
  Pre-defining them here prevents different agents from picking arbitrary
  L3 paths while missing the actual critical user journeys.

| Path Name            | Description                                | Health Indicator       |
|----------------------|--------------------------------------------|------------------------|
| _e.g., Feed Load_   | _User opens app, feed renders with items_  | _API 200 + non-empty_  |
| _e.g., Search_      | _User searches, results appear < 2s_       | _API 200 + latency_    |
| _e.g., Chat_        | _User sends message, response streams_     | _Stream start < 3s_    |

---

## 4. Critical Connectors **[recommended]**

| Connector            | Type       | Endpoint / Config                | Timeout  | Fallback     |
|----------------------|------------|----------------------------------|----------|--------------|
| _e.g., PostgreSQL_  | Database   | `DATABASE_URL`                   | 5s       | _read replica_ |
| _e.g., Qdrant_      | Vector DB  | `QDRANT_HOST:6333`               | 3s       | _graceful degrade_ |
| _e.g., Qwen API_    | LLM        | `QWEN_API_KEY`                   | 30s      | _queue retry_ |

---

## 5. Validation Commands **[required]**

| Check                | Command                                   | Expected        |
|----------------------|-------------------------------------------|-----------------|
| Lint                 | _e.g., `ruff check .`_                   | 0 errors        |
| Type Check           | _e.g., `mypy services/`_                 | 0 errors        |
| Unit Tests           | _e.g., `pytest tests/unit -q`_           | all pass        |
| Integration Tests    | _e.g., `pytest tests/integration -q`_    | all pass        |
| Build                | _e.g., `pnpm build`_                     | exit 0          |
| Health Probe         | _e.g., `curl http://localhost:8000/health`_ | `{"ok": true}` |

---

## 6. Health Endpoints **[recommended]**

| Endpoint             | Method | Expected Response                | Check Interval |
|----------------------|--------|----------------------------------|----------------|
| _e.g., `/health`_   | GET    | `200 {"ok": true}`               | 60s            |
| _e.g., `/ready`_    | GET    | `200 {"ready": true}`            | 30s            |

---

## 7. Rollback Assets **[required for T2+]**

| Asset                | Location / Command                        | Notes            |
|----------------------|-------------------------------------------|------------------|
| Previous deploy      | _e.g., `git revert` or CI rollback_      | _always available_ |
| Database migration   | _e.g., `alembic downgrade -1`_           | _if applicable_  |
| Config rollback      | _e.g., restore from env backup_          | _manual step_    |

---

## 8. Alert Channels **[recommended]**

| Severity | Channel              | Escalation Path               |
|----------|----------------------|-------------------------------|
| SEV-1    | _e.g., phone/SMS_   | _immediate human escalation_  |
| SEV-2    | _e.g., Slack #ops_  | _within 1h_                   |
| SEV-3    | _e.g., email_       | _next business cycle_         |

---

## 9. Project-Specific Overrides

Use this section to document any project-specific deviations from the
default AgentSDLC rules.

| Override             | Justification                              | Approved By      |
|----------------------|--------------------------------------------|------------------|
| _e.g., Class D without human sign-off_ | _compensating control: ..._ | _human operator_ |

---

## 10. Notes

_Any additional context, known constraints, or temporary workarounds._
