# Bootstrap Governance Profiles for IEF v0

This document defines the three bootstrap governance profiles used during IEF v0. It translates [RFC-0004: Bootstrap Governance Profiles](https://github.com/everwork-ai/IEF-Program/blob/main/rfcs/RFC-0004-bootstrap-governance-profile.md) and [ADR-0006: Use Bootstrap Governance Profiles](https://github.com/everwork-ai/IEF-Program/blob/main/decisions/ADR-0006-use-bootstrap-governance-profiles.md) into concrete rules, evidence requirements, and gate constraints for the IEF-Governance layer.

## References

- `IEF-Program#6` — P1-Contracts execution epic
- `IEF-Protocol#2` — Shared object model definition (constrained by this document)
- `IEF-Operations#2` — Task and run lifecycle definition (constrained by this document)
- `IEF-Program/docs/IEF_DEFINITION_OF_DONE.md`
- `IEF-Program/docs/IEF_GITHUB_WORKFLOW.md`
- `governance/LIFECYCLE_CONTRACT.md`
- `governance/STAGE_GATES.md`

## Purpose and Scope

**Governance owns:**
- Rules, gates, acceptance criteria, evidence requirements, and review requirements.
- Classification of changes into the correct governance profile.
- Human sign-off rules and escalation conditions.

**Governance does NOT own:**
- Task lifecycle definitions (owned by IEF-Operations).
- Protocol schema definitions (owned by IEF-Protocol).
- Runner, adapter, or automation implementations (owned by downstream repos).
- Project control center logic (owned by IEF-Program).

## Profile Summary

| Profile | Use For | Approval |
|---|---|---|
| **Design-Lite** | README updates, doc stubs, non-normative comments, structural reorganization that does not change runtime behavior | One reviewer check |
| **Contract-Critical** | Protocol objects, task lifecycle definitions, runner interfaces, adapter contracts, knowledge context formats, governance profiles, and any change that downstream repos depend on | Cross-review + Program Agent or human sign-off |
| **Implementation-Controlled** | Code, scripts, validators, runner logic, GitHub automation, adapter code, and any change that affects runtime behavior | Review before merge; Program Agent or human approval for high-risk changes; Section 6 human sign-off mandatory when applicable |

---

## 1. Design-Lite

### Scope
- README, markdown, or inline documentation updates
- Non-normative comments and clarifications
- Structural reorganization (files moved without content change to contracts)
- Status reports, meeting notes, and administrative records

### Non-Scope
- Any change that defines or modifies a contract, schema, interface, or state machine
- Any change to a governance profile
- Any change that affects runtime behavior, CI/CD, or automation
- Cross-repo boundary changes

### Required Evidence
- [ ] Linked issue exists
- [ ] Acceptance criteria are stated in the issue
- [ ] Pull request is opened
- [ ] Reviewer has checked the change
- [ ] No broken links or obvious errors

### Review Requirements
- One reviewer check (can be a peer agent or human).
- Reviewer confirms scope is truly Design-Lite and no hidden contract changes are present.

### Human Sign-off Required
No.

### Program Agent Sign-off Required
No.

### Pre-Merge Checklist
- [ ] Issue is linked in PR description
- [ ] PR title follows convention: `docs(scope): description`
- [ ] Reviewer approved or commented with explicit check
- [ ] No broken links (spot-check)
- [ ] No normative schema, interface, or lifecycle changes introduced

### Typical Examples
- Fix a typo in `README.md`
- Add a doc comment to a non-contract file
- Reorganize folder structure under `docs/` without changing content
- Update a status report

### Escalation Conditions
Escalate to **Contract-Critical** when:
- The change begins to define or modify a contract, schema, state machine, gate, interface, or cross-repo boundary.
- The change affects what another repo is allowed to assume or implement.
- The change modifies any file in `governance/`, `instantiation/`, or `operations/` that carries normative weight.

**Default rule:** If the correct profile is unclear, default to **Contract-Critical**.

---

## 2. Contract-Critical

### Scope
- Protocol objects and shared schemas (e.g., `AgentCard`, `TaskEnvelope`, `RunEvent`, `ArtifactRef`, `ContextRef`)
- Task lifecycle state definitions and transitions
- Runner interfaces and adapter contracts
- Knowledge context formats and memory contracts
- Governance profiles, stage gates, and acceptance criteria
- Cross-repo dependency rules and boundary definitions
- Any change that downstream repos (Runners, Knowledge, Adapters) must conform to

### Non-Scope
- Implementation of a runner, adapter, or validator
- GitHub Actions or CI/CD scripting (unless the Action enforces a contract)
- Pure documentation that does not define a contract
- Internal refactoring with no external boundary impact

### Required Evidence
- [ ] Linked issue exists
- [ ] Design document or RFC reference (e.g., RFC-0004, an ADR, or an explicit design section in the PR)
- [ ] Explicit input/output contract documented
- [ ] Explicit boundary documented (what it owns / does not own)
- [ ] Schema, example, or validation rule provided
- [ ] Cross-review from at least one other repo perspective
- [ ] Rollback plan documented if replacing an existing contract

### Review Requirements
- Cross-review: at least one reviewer from a different capability area or repo perspective.
- Program Agent or human approval before merge.
- Review must confirm layer boundaries are respected and no implementation leakage occurred.

### Human Sign-off Required
- For changes to IEF top-level boundary definitions
- For changes to the repo matrix or ownership model
- For changes to governance profiles themselves (meta-changes)
- For changes to Protocol or Operations core contracts that affect multiple repos
- For cross-repo architecture changes with broad blast radius
- For any modification that introduces rollback risk to existing data or state contracts

### Program Agent Sign-off Required
- Yes, before merge, unless human sign-off is explicitly substituted.
- For Contract-Critical PRs in IEF-Protocol#2 and IEF-Operations#2, Program Agent sign-off is mandatory.

### Pre-Merge Checklist
- [ ] Issue is linked in PR description
- [ ] Design reference is cited
- [ ] Input/output contract is explicit
- [ ] Boundary statement is present
- [ ] Schema, example, or validation rule is attached
- [ ] Cross-review from another repo perspective is completed
- [ ] Program Agent or human approval is recorded
- [ ] Rollback plan is documented (if replacing existing contract)
- [ ] No implementation code or runner logic is mixed into the contract definition

### Typical Examples
- Define JSON schema for `TaskEnvelope`
- Define task lifecycle state machine in IEF-Operations
- Define runner interface contract
- Define adapter input/output contract
- Add or modify a governance profile (this document)
- Define cross-repo dependency rules

### Escalation Conditions
Escalate to **Implementation-Controlled** when:
- The change begins to include code, scripts, validators, automation, CI/CD workflows, runner logic, or adapter implementations.
- The change affects a live runner, adapter, GitHub Action, or deployed automation.
- The change introduces a new automation that enforces or validates a contract at runtime.

---

## 3. Implementation-Controlled

### Scope
- Code, scripts, and validators
- Runner logic and adapter implementations
- GitHub Actions, CI/CD pipelines, and repository automation
- State machine implementations (as opposed to state machine definitions)
- Any change that modifies runtime behavior, execution paths, or deployed infrastructure

### Non-Scope
- Contract or schema definitions (those are Contract-Critical)
- Pure documentation without runtime effect
- Reorganization that does not change behavior

### Required Evidence
- [ ] Linked issue exists
- [ ] Design reference (RFC, ADR, or contract doc)
- [ ] Pull request is opened
- [ ] Multi-level test evidence attached to PR (see Test Level Requirements below)
- [ ] Rollback plan documented
- [ ] Review before merge
- [ ] No regression in existing capabilities

#### Test Level Requirements for Implementation-Controlled

Implementation-Controlled changes affect runtime behavior and must provide evidence at the following test levels, aligned with `governance/STAGE_GATES.md`:

| Risk Level | Minimum Test Levels | L3 Required |
|---|---|---|
| Standard runtime-impacting changes / T2 / G-Std | L1 + L2 + at least one L3 path | Required |
| High-risk / T3 / cross-repo / stateful / memory / scheduler / permission | L1 + L2 + at least one L3 path, with stronger canonical workflow coverage | Required |

**Test level definitions:**
- **L1 — Unit / local validation:** Single function or module verified in isolation.
- **L2 — Integration validation:** Cross-module, API contract, or component interaction verified.
- **L3 — End-to-end / system / canonical workflow:** Full user or system path verified end-to-end.

**Dry-run policy:**
- Dry-run evidence is permitted only as **supplemental** evidence.
- Dry-run alone is **not sufficient** to satisfy the test requirement for Implementation-Controlled changes.
- Dry-run cannot replace L1, L2, or the required L3 path for Implementation-Controlled runtime changes.
- If live L3 execution is unavailable, a reviewer may accept a simulated/canonical workflow L3 only if it exercises the same end-to-end contract path and the limitation is explicitly documented.

**L3 exemption:**
- L3 exemption is only allowed for changes that are classified as Implementation-Controlled but do not affect executable/runtime behavior, or where no meaningful end-to-end path exists.
- Exemption must be explicitly justified in the PR and accepted by reviewer plus Program Agent.
- Exemption is not allowed for runtime-impacting T2/T3 changes, high-risk, cross-repo, stateful, memory, scheduler, permission, runner, adapter, or automation changes.

#### Contract Evidence Rules for Implementation-Controlled

- **Default:** An Implementation-Controlled PR must **reference an approved contract or design document**. It does not need to redefine the schema.
- **If the PR adds or modifies a contract:** Explicit input/output, boundary statement, schema/example, and validation rule become **mandatory** (equivalent to Contract-Critical evidence requirements).
- **If the PR only implements an existing contract:** Test evidence and contract reference are sufficient. Schema redefinition is not required.

### Review Requirements
- Review before merge by at least one reviewer.
- For high-risk changes, Program Agent or human approval is required.
- If the change falls under **Section 6 (Human Sign-off Rules)**, **human owner sign-off is mandatory** and cannot be substituted by Program Agent approval alone.
- Review must confirm the implementation matches the referenced contract and does not silently broaden scope.

### Human Sign-off Required
- For high-risk automation (e.g., auto-merge bots, auto-deployment, self-modifying workflows)
- For changes to core agent loop or scheduler logic
- For permission or deletion-path changes
- For changes that affect production data or state with rollback risk
- For any Class D change per `governance/LIFECYCLE_CONTRACT.md` Section 3.4

### Program Agent Sign-off Required
- For high-risk changes as defined above.
- Optional but recommended for standard Implementation-Controlled changes.
- **Cannot substitute for Section 6 human sign-off when Section 6 applies.**

### Pre-Merge Checklist
- [ ] Issue is linked in PR description
- [ ] Design reference is cited
- [ ] PR includes multi-level test evidence (L1 + L2 + at least one L3 path for runtime-impacting changes)
- [ ] Rollback plan is documented with specific commands or steps
- [ ] Reviewer approved
- [ ] Program Agent or human approval recorded for high-risk changes
- [ ] Human sign-off recorded if Section 6 applies
- [ ] No regression in existing capabilities (existing tests pass or explicit justification provided)
- [ ] Contract definitions are not mixed into implementation code without separate Contract-Critical review

### Typical Examples
- Implement a runner that consumes `TaskEnvelope`
- Add a validation script for schema conformance
- Add a GitHub Action that checks PR boundaries
- Modify adapter code to call a new protocol endpoint
- Add state machine execution logic

### Escalation Conditions
- If a change during review is found to also modify a contract, split the contract change into a separate Contract-Critical PR.
- If rollback risk is higher than initially estimated, require human sign-off before merge.

---

## 4. Evidence Requirements Matrix

| Evidence | Design-Lite | Contract-Critical | Implementation-Controlled |
|---|---|---|---|
| Linked issue | Required | Required | Required |
| Acceptance criteria in issue | Required | Required | Required |
| Design doc / RFC reference | — | Required | Required |
| Explicit input/output contract | — | Required | Required only if adding/modifying contract; otherwise reference existing contract |
| Explicit boundary statement | — | Required | Required only if adding/modifying contract; otherwise reference existing contract |
| Schema / example / validation rule | — | Required | Required only if adding/modifying contract; otherwise reference existing contract |
| Cross-review from other repo | — | Required | Recommended |
| PR opened | Required | Required | Required |
| Multi-level test evidence (L1 + L2 + at least one L3 path for runtime-impacting IC) | — | Recommended | Required |
| Dry-run (supplemental only) | — | Allowed | Allowed |
| Rollback plan | — | Required if replacing contract | Required |
| Reviewer check | Required | Required | Required |
| Program/human approval before merge | — | Required | Required for high-risk |
| Human sign-off (Section 6 categories) | — | Required if applicable | Required if applicable |
| No regression check | — | Recommended | Required |

**Note on contract evidence for Implementation-Controlled:** If an Implementation-Controlled PR introduces a new contract or modifies an existing one, the evidence bar rises to Contract-Critical for the contract portion. The PR should either (a) split the contract change into a separate Contract-Critical PR, or (b) provide full contract evidence inline and accept the higher review bar.

---

## 5. Review Requirements Matrix

| Profile | Minimum Review | Approval Authority |
|---|---|---|
| Design-Lite | One reviewer check | Reviewer |
| Contract-Critical | Cross-review (different repo or capability perspective) + Program Agent or human approval | Program Agent or human |
| Implementation-Controlled | Reviewer approval; high-risk changes need Program Agent or human approval; Section 6 categories need human sign-off | Reviewer (standard); Program Agent or human (high-risk); **Human owner (Section 6)** |

**Cross-review definition:** A reviewer who is not the author and who represents a different IEF layer or repository perspective. For example, an IEF-Protocol change should be reviewed from an IEF-Operations or IEF-Runners perspective.

**Important:** Program Agent approval never substitutes for human sign-off required by Section 6. When Section 6 applies, both Program Agent and human sign-off may be required, but human sign-off is the non-negotiable minimum.

---

## 6. Human Sign-off Rules

Human owner sign-off is **required** before merge for the following categories, regardless of whether a Program Agent has also approved:

1. **IEF top-level boundary changes** — modifications to what each layer (Governance, Protocol, Operations, Program, Runners, Knowledge, Adapters) is allowed to own or implement.
2. **Repo matrix changes** — adding, removing, or reassigning repositories in the IEF ecosystem.
3. **Governance profile changes** — modifications to this document or to `docs/IEF_V0_GATE_PROFILE.md`.
4. **Protocol / Operations core contract changes** — changes to `AgentCard`, `TaskEnvelope`, `RunEvent`, `ArtifactRef`, `ContextRef`, or the task lifecycle state machine that affect multiple downstream repos.
5. **High-risk automation** — GitHub Actions, bots, or scripts that auto-merge, auto-deploy, auto-close issues, or otherwise reduce human oversight.
6. **Cross-repo architecture changes** — changes with blast radius across three or more IEF repos.
7. **Rollback-risk data or state changes** — modifications that could corrupt, lose, or irreversibly alter existing task state, artifact references, or runtime memory.

**Process:** The human sign-off must be recorded as an explicit approving review or comment on the PR, addressing:
- What is being changed
- Why the change is justified
- What the rollback path is
- What monitoring will verify safety

**Relation to Program Agent approval:** Program Agent approval is a separate gate. When Section 6 applies, Program Agent approval may coexist with human sign-off, but it **cannot replace it**.

---

## 7. Profile Escalation Rules

### Design-Lite → Contract-Critical
Escalate when the change:
- Begins to define or modify a contract, schema, state machine, gate, interface, or cross-repo boundary.
- Introduces a normative rule that another repo must follow.
- Touches files in `governance/`, `instantiation/`, or `operations/` that carry normative weight.

### Contract-Critical → Implementation-Controlled
Escalate when the change:
- Begins to include code, scripts, automation, runner logic, adapter code, or CI/CD workflows.
- Affects runtime behavior or deployed infrastructure.
- Introduces an enforcement mechanism (validator, checker, Action) for a contract.

### Default Rule
- **If the correct profile is unclear, default to Contract-Critical.**
- During review, if a profile mismatch is discovered, the reviewer must request reclassification before approving.
- It is permissible to split a single issue into two PRs (one Contract-Critical for the contract, one Implementation-Controlled for the code) to keep boundaries clean.

---

## 8. Cross-Repository Constraints

This section makes the governance profiles binding on sibling repositories.

### IEF-Program#6
- IEF-Program#6 is the P1-Contracts epic that coordinates Governance#2, Protocol#2, and Operations#2.
- All three issues may draft in parallel, but their PRs must reference each other and must not duplicate definitions.
- Governance defines the profiles; Program defines the control interface and coordinates execution.

### IEF-Protocol#2
- Any PR for `IEF-Protocol#2` **must satisfy the Contract-Critical profile** at minimum.
- Any modification of `AgentCard`, `TaskEnvelope`, `RunEvent`, `ArtifactRef`, or `ContextRef` is classified as **Contract-Critical**.
- Protocol schema PRs require cross-review from at least one of IEF-Operations or IEF-Runners.
- **Field-level boundary:** Protocol defines the object schema and field types. Operations may constrain the allowed values of lifecycle-related fields (e.g., `TaskEnvelope.status`) without redefining the object itself. Either layer changing the field definition requires Contract-Critical review.

### IEF-Operations#2
- Any PR for `IEF-Operations#2` **must satisfy the Contract-Critical profile** at minimum.
- Task lifecycle state definitions and transitions are **Contract-Critical**.
- If Operations#2 introduces automation, validators, or runner-adjacent scripts, those parts of the PR must satisfy **Implementation-Controlled** evidence requirements.
- Operations may reference Protocol objects by name and constrain specific field values, but must not duplicate object definitions.

### Downstream Repos (Blocked)
- IEF-Runners#2, IEF-Knowledge#2, and IEF-Adapters#2 are **blocked** from implementation until Governance#2, Protocol#2, and Operations#2 have produced draft contracts.
- When downstream repos begin work, their contract-definition PRs must be **Contract-Critical** and their implementation PRs must be **Implementation-Controlled**.

---

## 9. Mandatory Checks for All Profiles

1. **Issue link** — Every PR must reference an issue.
2. **No direct main push** — All delivery via PR.
3. **Layer boundary respect** — Do not implement another layer's responsibility in your PR.
4. **IEF Command Center visibility** — Every issue should be visible in the project board when the board is active.

---

## 10. Boundary Statement

**Governance owns:**
- Governance profiles and their escalation rules
- Gate definitions and pass criteria
- Evidence and review requirements
- Human sign-off rules
- Cross-repo constraint specifications

**Governance does NOT own:**
- Task lifecycle definitions (IEF-Operations)
- Protocol schema definitions (IEF-Protocol)
- Runner, adapter, or automation implementations (downstream capability repos)
- Project control center or execution coordination (IEF-Program)

**Cross-layer field boundary:**
- Protocol owns the object schema (field names, types, structures).
- Operations owns lifecycle semantics (allowed state values, transition rules).
- Neither layer may silently redefine fields owned by the other. Changes to shared fields require Contract-Critical cross-review.

Any PR that drifts into another layer's responsibility must be split or re-scoped.

---

## 11. Change Log

| Date | Change | Profile |
|---|---|---|
| 2026-04-28 | Initial bootstrap profiles defined | Contract-Critical |
| 2026-04-28 | Aligned with Protocol#2: added AgentCard, field-level boundary note | Contract-Critical |
| 2026-04-28 | Fixed Codex review findings: tightened IC test requirements, reconciled G3 contract evidence, clarified approval authority | Contract-Critical |
| 2026-04-28 | Fixed Codex P1: raised IC test minima to L1+L2+L3 for all runtime-impacting work; tightened dry-run and L3 exemption rules | Contract-Critical |
