# IEF Architecture Context

> This document explains how this repository fits into the IEF (Intelligent Employee Framework) repository family.

## One-sentence role inside IEF

Defines what acceptable AI work looks like -- the rule and decision layer for governance, approval, review, and risk classification.

## What this repository owns

- Governance packs (SDLC is the primary pack)
- Review rules and approval policies
- Audit rules and exception handling
- Promotion and rollback policies
- Model routing policy
- Risk classification framework

## What this repository does NOT own

- Primary task state (IEF-Work)
- Concrete executor implementations (IEF-Workers)
- Protocol transport schemas (IEF-Protocol)
- Long-term knowledge storage (IEF-Knowledge)

## Dependencies on other IEF repos

| Dependency | Direction | Purpose |
|---|---|---|
| IEF-Work | produced | Policy requirements fed into task lifecycle |
| IEF-Workers | produced | Policy constraints for worker execution |
| IEF-Adapters | produced | Governance packs for host-environment rule generation |
| IEF-Knowledge | consumed | Historical decisions, incidents, playbook references |

## Migration notes

This repository was formerly named `AgentSDLC`. It was renamed to `IEF-Governance` as part of the IEF repository family reorganization (IEF Design Pack v0.2, section 19.1).

- `SDLC` is now the **primary governance pack** inside this repository, not the top-level name.
- All existing governance history is preserved.
- No second parallel governance repository was created.

## Related repositories

| Repository | Role |
|---|---|
| [IEF-Governance](https://github.com/brantzh6/IEF-Governance) | This repo -- governance layer |
| [IEF-Knowledge](https://github.com/brantzh6/IEF-Knowledge) | Memory and knowledge layer |
| [IEF-Work](https://github.com/brantzh6/IEF-Work) | Task and run state layer |
| [IEF-Protocol](https://github.com/brantzh6/IEF-Protocol) | Communication contracts |
| [IEF-Workers](https://github.com/brantzh6/IEF-Workers) | Concrete worker executors |
| [IEF-Adapters](https://github.com/brantzh6/IEF-Adapters) | Host-environment bindings |

## Full architecture reference

See [IEF Design Pack v0.2](https://github.com/brantzh6/IEF-Governance/blob/main/docs/IEF_Design_Pack_v0.2.md) for the complete architecture specification.
