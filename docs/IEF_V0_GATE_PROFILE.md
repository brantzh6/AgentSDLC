# IEF v0 Gate Profile

This document defines the seven stage gates for IEF v0. It adapts the AgentSDLC gate model (`governance/LIFECYCLE_CONTRACT.md` and `governance/STAGE_GATES.md`) into a lightweight but constrained bootstrap form.

**Philosophy:** All seven gates always exist. The gate profile determines the strictness and evidence form of each gate, not whether it applies. In v0, gates are deliberately lean to avoid process overhead, but they are mandatory and non-bypassable.

## References

- `docs/BOOTSTRAP_GOVERNANCE_PROFILE.md` — Profile definitions and evidence requirements
- `governance/LIFECYCLE_CONTRACT.md` — Full lifecycle contract (AgentSDLC)
- `governance/STAGE_GATES.md` — Gate quick-reference card
- `IEF-Program#6` — P1-Contracts execution epic
- `IEF-Protocol#2` — Shared object model definition
- `IEF-Operations#2` — Task and run lifecycle definition

## Gate-to-Profile Matrix

| Gate | Design-Lite | Contract-Critical | Implementation-Controlled |
|------|-------------|-------------------|---------------------------|
| G1 Scope Gate | Lite — issue + criteria | Standard — issue + criteria + classification | Standard — issue + criteria + classification |
| G2 Boundary Gate | Lite — profile check | Standard — profile + cross-repo boundary | Standard — profile + cross-repo boundary + runtime boundary |
| G3 Contract Gate | Null-form pass — confirm no contract, schema, interface, lifecycle, or gate is changed | Required — design + I/O + schema | Required — reference approved contract; mandatory I/O + schema only if adding/modifying contract |
| G4 Evidence Gate | Lite — PR + no broken links | Standard — PR + schema + cross-review readiness | Standard — PR + L1+L2+L3 multi-level test evidence + rollback plan |
| G5 Review Gate | Lite — one reviewer check | Standard — cross-review + Program/human approval | Standard — reviewer + Program/human for high-risk; human sign-off mandatory for Section 6 categories |
| G6 Merge Gate | Lite — checklist + merge | Standard — checklist + approval + rollback known | Standard — checklist + approval + rollback verified; human sign-off for Section 6, Program/human for other high-risk |
| G7 Learning Gate | Lite — spot-check | Standard — observation + issue closure rules | Standard — observation + regression watch + durable improvement |

## Mapping to AgentSDLC Gates

| IEF v0 Gate | AgentSDLC Gate | Relationship |
|---|---|---|
| G1 Scope Gate | G1 Design Complete | Scope and acceptance criteria are the v0 minimum for "design complete" |
| G2 Boundary Gate | G2 Design Approved | Boundary and profile selection replace the full design review in v0 |
| G3 Contract Gate | G3 Implementation Done | For contracts, "implementation" means contract definition is complete |
| G4 Evidence Gate | G4 Code Review Passed | Evidence is what the reviewer evaluates; no evidence, no review |
| G5 Review Gate | G4 Code Review Passed | Review is the human/agent checkpoint on evidence and correctness |
| G6 Merge Gate | G5 Testing Passed + G6 Production Deployed | In v0, merge is the proxy for "deployed" because changes are docs/contracts/code in repos |
| G7 Learning Gate | G7 Monitoring Active | Post-merge observation closes the feedback loop |

---

## G1 Scope Gate

**Goal:** Confirm the change is bounded, has a linked issue, and acceptance criteria are clear.

**Input:**
- GitHub issue with title, description, and acceptance criteria
- Governance profile declaration (Design-Lite / Contract-Critical / Implementation-Controlled)

**Checklist:**
- [ ] Linked issue exists in the correct repository
- [ ] Acceptance criteria are stated in the issue body
- [ ] Scope is bounded (one task, one scope, one result)
- [ ] Task classification is recorded for Contract-Critical and Implementation-Controlled changes
- [ ] No blocker or unresolved dependency that prevents starting

**Pass Criteria:**
- All checklist items are satisfied.
- The issue is added to the IEF Command Center project board when the board is active.

**Failure Handling:**
- Return to issue refinement.
- Do not create a branch or open a PR until G1 passes.
- If scope is unbounded, split into multiple issues.

**Applicable Profile:** All profiles.

---

## G2 Boundary Gate

**Goal:** Confirm layer boundaries are respected and the correct governance profile is selected.

**Input:**
- Issue with proposed change description
- Identified affected files and surfaces
- Selected governance profile

**Checklist:**
- [ ] Governance profile is selected per `docs/BOOTSTRAP_GOVERNANCE_PROFILE.md`
- [ ] Layer boundary is respected (Governance does not define schema; Protocol does not define lifecycle; Operations does not define governance)
- [ ] No hidden contract changes are smuggled into a Design-Lite PR
- [ ] Cross-repo impact is assessed for Contract-Critical and Implementation-Controlled changes
- [ ] If the correct profile is unclear, the default escalation to Contract-Critical is applied

**Pass Criteria:**
- Profile selection is correct or explicitly escalated.
- No boundary violation is present.

**Failure Handling:**
- Reclassify the profile or rescope the change.
- Split the PR if it mixes profiles (e.g., contract definition + implementation code).
- Reject PRs that violate layer boundaries.

**Applicable Profile:** All profiles. Design-Lite uses a lightweight self-check; Contract-Critical and Implementation-Controlled require explicit boundary documentation.

---

## G3 Contract Gate

**Goal:** For Contract-Critical and Implementation-Controlled changes, ensure the contract, design, or interface is well-defined before implementation or merge proceeds.

**Input:**
- Design document, RFC reference, or explicit contract section in the PR
- For contract changes: schema, example, or validation rule

**Checklist:**
- [ ] Design reference is cited (RFC, ADR, or design doc)
- [ ] Explicit input/output contract is documented
- [ ] Explicit boundary is documented (what it owns / does not own)
- [ ] Schema, example, or validation rule is provided
- [ ] Cross-repo impact is assessed (which downstream repos are affected)
- [ ] For replacement of an existing contract: rollback plan is documented

**Pass Criteria:**
- Contract definition is complete, reviewable, and free of placeholders.
- For Implementation-Controlled changes:
  - The referenced contract has already passed G3 in a prior Contract-Critical PR, **or**
  - The contract is defined inline with equivalent rigor.
  - If the Implementation-Controlled PR **adds or modifies** a contract, explicit I/O, schema, and validation rule are **mandatory** (same bar as Contract-Critical).
  - If the Implementation-Controlled PR **only implements** an existing contract, a contract reference is sufficient; schema redefinition is not required.
- For Design-Lite changes: the null-form pass is confirmed — the PR does not introduce, modify, or remove any contract, schema, interface, lifecycle definition, or gate.

**Failure Handling:**
- Return to design. Do not approve a Contract-Critical PR that lacks explicit I/O or boundary.
- Do not approve an Implementation-Controlled PR that implements an undefined or unreviewed contract.
- Do not approve a Design-Lite PR that smuggles contract changes; reclassify to Contract-Critical.

**Applicable Profile:**
- **Contract-Critical:** Required.
- **Implementation-Controlled:** Required (contract must be defined or referenced; mandatory I/O+schema only if new/modified contract).
- **Design-Lite:** Not skipped. Passed in **null form** — the gate is satisfied by confirming the PR has no contract impact.

---

## G4 Evidence Gate

**Goal:** Ensure required evidence exists before review begins.

**Input:**
- Pull request with attached evidence
- Per-profile evidence checklist

**Checklist:**
- [ ] Linked issue is referenced in the PR description
- [ ] For **Design-Lite:** PR is opened and reviewer can inspect; no broken links or obvious errors
- [ ] For **Contract-Critical:** design reference, explicit I/O, boundary statement, schema/example are present; required cross-reviewers or review perspectives are identified
- [ ] For **Implementation-Controlled:**
  - [ ] Design reference (or inline contract if new/modified)
  - [ ] Multi-level test evidence (L1 + L2 + at least one L3 path for runtime-impacting Implementation-Controlled changes)
  - [ ] High-risk / cross-repo / stateful / memory / scheduler / permission changes require stronger canonical workflow L3 coverage
  - [ ] Dry-run evidence, if present, is explicitly marked as **supplemental** and does not substitute for required test levels
  - [ ] Rollback plan is documented with specific commands or steps
- [ ] No placeholder sections (e.g., "TODO: add test") remain
- [ ] Evidence is verifiable (links work, commands are reproducible, outputs are shown)

**Pass Criteria:**
- Evidence is complete for the selected profile.
- Reviewer can independently verify the evidence without guessing.
- G4 does not require completed cross-review.
- G4 only requires the PR to be ready for cross-review (reviewers identified, perspectives stated).
- Completed cross-review is a G5 Review Gate requirement.
- Dry-run alone is **not sufficient**.
- L3 path is required for runtime-impacting Implementation-Controlled changes.
- L3 exemption requires explicit justification and reviewer plus Program Agent acceptance, and is not allowed for T2/T3 runtime-impacting or high-risk categories.

**Failure Handling:**
- Request evidence from the author.
- Block review until G4 passes.
- Reject PRs that claim completion without evidence.

**Applicable Profile:** All profiles. Strictness scales with profile.

---

## G5 Review Gate

**Goal:** Ensure review is completed per profile requirements.

**Input:**
- Pull request with complete evidence (G4 passed)
- Reviewer(s) assigned

**Checklist:**
- [ ] For **Design-Lite:** one reviewer has performed an explicit check and confirmed no hidden contract changes
- [ ] For **Contract-Critical:** cross-review from at least one other repo perspective is completed; findings are addressed
- [ ] For **Implementation-Controlled:**
  - [ ] Reviewer approval is recorded
  - [ ] For high-risk changes: Program Agent or human approval is recorded
  - [ ] For changes falling under `docs/BOOTSTRAP_GOVERNANCE_PROFILE.md` **Section 6 (Human Sign-off Rules)**: **human owner sign-off is recorded** and cannot be substituted by Program Agent approval alone
- [ ] Review result is explicit (approve / conditional approve / reject)
- [ ] No unresolved blockers remain

**Pass Criteria:**
- Review is complete with explicit approval.
- All required approvals (reviewer, cross-reviewer, Program/human, human owner for Section 6) are recorded.

**Failure Handling:**
- Address findings and re-submit for review.
- If scope creep is discovered during review, return to G1/G2.
- If profile mismatch is discovered, reclassify before merge.

**Applicable Profile:** All profiles. Review depth scales with profile.

---

## G6 Merge Gate

**Goal:** Ensure pre-merge conditions are satisfied and rollback is known before the change enters `main`.

**Input:**
- Approved pull request (G5 passed)
- Merge checklist

**Checklist:**
- [ ] All prior gates (G1–G5) are passed for the selected profile
- [ ] No direct push to `main`; delivery is via PR only
- [ ] For **Contract-Critical replacing existing contract:** rollback plan is documented and accessible
- [ ] For **Implementation-Controlled:** rollback plan is documented with specific commands or steps
- [ ] For changes falling under `docs/BOOTSTRAP_GOVERNANCE_PROFILE.md` **Section 6**: **human owner sign-off is recorded** (Program Agent approval does not substitute)
- [ ] For other high-risk changes: Program Agent or human approval is recorded
- [ ] No regression in existing capabilities (existing tests pass, or explicit justification is provided)
- [ ] PR title and description are accurate and link the issue (`Closes #N`)

**Pass Criteria:**
- All merge checklist items are satisfied.
- Rollback path is known (T1/Design-Lite), documented (T2/Contract-Critical), or verified (T3/Implementation-Controlled with high risk).

**Failure Handling:**
- Do not merge if any checklist item is unresolved.
- If a critical flaw is discovered at G6, return to G3 or G4.
- For emergency bypass, document the exception and create a follow-up issue.

**Applicable Profile:** All profiles. Merge discipline applies universally.

---

## G7 Learning Gate

**Goal:** Close the feedback loop after merge. Observe outcomes, capture lessons, and feed findings into the next cycle.

**Input:**
- Merged change in `main`
- Usage, runtime, or downstream consumption observations

**Checklist:**
- [ ] For **Design-Lite:** spot-check that the change appears correctly and no unintended side effects are visible
- [ ] For **Contract-Critical:** downstream repos can consume the contract without confusion; at least one downstream issue or PR references the contract
- [ ] For **Implementation-Controlled:** post-merge observation period is defined; monitoring signals are reviewed; no regression is detected
- [ ] Anomalies are converted into structured issues with source, evidence, and category
- [ ] For recurring issues: at least one durable improvement is identified (checklist, test, guardrail, runbook, or design constraint)
- [ ] Issue closure requires confirmation that the change is stable, not just that the PR is merged

**Pass Criteria:**
- Change is confirmed stable or anomalies are tracked as actionable issues.
- Lessons are captured and linked to the original issue or a follow-up.

**Failure Handling:**
- Create a follow-up issue if post-merge problems are detected.
- Consider rollback if the merged change causes regression or blocks downstream work.
- Feed findings into the next G1 as a monitoring-generated task.

**Applicable Profile:** All profiles. Observation depth scales with profile.

---

## Gate Flowchart

```text
Issue created
  |
  v
G1 Scope Gate — issue linked, acceptance criteria clear?
  | NO -> Refine issue
  | YES
  v
G2 Boundary Gate — correct profile? layer boundaries respected?
  | NO -> Reclassify or rescope
  | YES
  v
G3 Contract Gate — contract defined (if Contract-Critical / Implementation-Controlled)?
  | NO -> Return to design
  | YES / null-form pass for Design-Lite
  v
G4 Evidence Gate — evidence complete for profile?
  | NO -> Add evidence
  | YES
  v
G5 Review Gate — review passed per profile?
  | NO -> Address findings
  | YES
  v
G6 Merge Gate — pre-merge checklist complete, rollback known?
  | NO -> Do not merge
  | YES -> Merge
  v
G7 Learning Gate — stable? lessons captured?
  | NO -> Follow-up issue; consider rollback
  | YES -> Close issue
```

---

## Cross-Repository Gate Constraints

### IEF-Program#6
- Program coordinates the P1-Contracts epic but does not override gate decisions made in sibling repos.
- Program Agent sign-off on Contract-Critical PRs satisfies the cross-repo approval requirement where applicable.

### IEF-Protocol#2
- Protocol#2 PRs must pass G3 Contract Gate with explicit schema, example, or validation rule.
- Protocol#2 PRs must pass G5 Review Gate with cross-review from IEF-Operations or IEF-Runners perspective.
- Modifications to `AgentCard`, `TaskEnvelope`, `RunEvent`, `ArtifactRef`, or `ContextRef` are treated as Contract-Critical.
- Protocol defines object schemas; Operations may constrain lifecycle-related field values (e.g., `TaskEnvelope.status`). Either layer changing the field definition requires Contract-Critical cross-review.

### IEF-Operations#2
- Operations#2 PRs must pass G3 Contract Gate with explicit lifecycle state definitions and transitions.
- Operations#2 PRs must pass G5 Review Gate with cross-review from IEF-Protocol or IEF-Runners perspective.
- If Operations#2 includes implementation (validators, scripts, automation), those parts must also satisfy Implementation-Controlled evidence requirements at G4.
- Operations may reference Protocol objects by name and constrain specific field values, but must not duplicate object definitions.

---

## Change Log

| Date | Change | Profile |
|---|---|---|
| 2026-04-28 | Initial v0 gate profile defined | Contract-Critical |
| 2026-04-28 | Aligned with Protocol#2: added AgentCard and field-level boundary note | Contract-Critical |
| 2026-04-28 | Fixed Codex review findings: null-form pass for Design-Lite G3, tightened IC test requirements, reconciled contract evidence, aligned approval authority | Contract-Critical |
| 2026-04-28 | Fixed Codex P1: raised IC test minima to L1+L2+L3 for all runtime-impacting work; tightened dry-run and L3 exemption rules | Contract-Critical |
| 2026-04-28 | Fixed Codex P1: removed completed cross-review from G4 Evidence Gate; cross-review readiness is required at G4, completed cross-review at G5 | Contract-Critical |
