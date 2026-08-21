---
name: change-spec
description: Generates a structured Change Specification document covering business justification, scope, risk, data impact, implementation plan, testing plan, and rollback steps. Use when proposing any code, API, database, infrastructure, or security change that requires a formal change record. Use when an engineer asks to "write a change spec", "document this change", "create a change request", or "spec this for review".
---

# Change Spec

## Overview

Produces a complete, wiki-ready Change Specification document for a proposed change. The document captures all information a reviewer needs to assess risk, validate testing coverage, and approve or reject the change — before any code is merged or deployed.

## When to Use

- Any change that needs a formal change record (code, API, DB, infra, config, security)
- Before opening a PR that affects production systems
- When a team or compliance process requires a change document
- Engineer says "write a change spec", "document this change", "create a change request"

**When NOT to use:** Pure refactors with no behaviour change that are already covered by an existing spec. Use `spec-driven-development` instead when no requirements exist yet and you need to define *what* to build first.

## Process

### Step 1: Gather Context

Infer all fields from the available context — open files, git diff, PR description, conversation history, and codebase. Do not ask the user for information. Use best-effort inference for every field:

1. **Summary** — One sentence: what is changing and why.
2. **Date** — Today's date, or the PR target date if known.
3. **Scope** — Which systems are touched (code, API, DB, infra, config, security).
4. **Risk level** — Derive from the risk table in Step 2.
5. **Data impact** — Infer from field names, entity types, or migration files in scope.
6. **Implementation steps** — Extract from the diff or conversation; order them.
7. **Testing approach** — Infer from test files touched or test types present in the codebase.
8. **Rollback** — Derive concrete revert steps from the implementation plan.
9. **Related controls** — Infer from PR labels, issue references, or commit messages if present.

If a field truly cannot be inferred, write `TBD` — never ask or block generation.

### Step 2: Assess Risk

Use this table to set the risk level:

| Condition | Risk |
|---|---|
| Isolated UI or logic change, no schema or auth touch | Low |
| New API endpoint, config change, dependency bump | Medium |
| Schema migration, auth change, external integration, data deletion | High |

Default checked box: **Low**. Override if conditions above indicate otherwise.

### Step 3: Write the Document

Produce output in this exact template. Fill every section — do not leave any section blank. If a field is genuinely not applicable, write "N/A" and one sentence explaining why.

````markdown
## Change Summary
<!-- Brief description of the requested change -->
{summary}
{date}

---

## Business Justification
<!-- Why is this change needed? What problem does it solve? -->
{justification}

---

## Scope of Change
<!-- Systems / services / components affected -->
- [{x_or_space}] Application Code
- [{x_or_space}] API
- [{x_or_space}] Database
- [{x_or_space}] Infrastructure
- [{x_or_space}] Configuration
- [{x_or_space}] Security Controls
- [{x_or_space}] Other: {other_scope}

---

## Risk Level
- [{low_x}] Low
- [{med_x}] Medium
- [{high_x}] High

---

## Data Impact
Does this change impact any of the following?
(PHI | PII not applicable)
- [{sensitive_x}] Sensitive/Internal Data
- [{no_data_x}] No Data Impact

---

## Implementation Plan
<!-- High-level steps for implementation -->
{implementation_steps}

---

## Testing Plan
<!-- How will this change be validated? -->
- [{unit_x}] Unit Testing
- [{int_x}] Integration Testing
- [{manual_x}] Manual Testing
- [{other_test_x}] Other: {other_test}

---

## Rollback Plan
<!-- Steps to revert this change if issues occur -->
{rollback_steps}

---

## Related Requirement / Control ID (Optional)
- [{policy_x}] Policy Requirement
- [{risk_x}] Risk Register Item
- [{incident_x}] Incident Follow-up
- [{adr_x}] Architecture Decision
- [{security_x}] Security Requirement
- [{customer_x}] Customer Requirement
- [{reliability_x}] Reliability Improvement
- [{compliance_x}] Compliance Control
- [{tech_debt_x}] Technical Debt w/ Risk Impact

---

## Change Requirements Checklist
- [ ] Code review required before merge
- [ ] Security impact assessed
- [ ] Testing completed prior to deployment
- [ ] Rollback plan documented
- [ ] Linked to Pull Request
````

### Step 4: Deliver a Single Copyable Block

Return exactly one fenced markdown block containing the completed document. Do not include commentary or headings outside the block. The output must be pasteable directly into a wiki, GitHub issue, or PR description.

## Output Guardrails

- Return exactly one fenced markdown block
- All checkboxes must be filled (`[x]` or `[ ]`) — never leave a checkbox ambiguous
- Risk level must have exactly one `[x]`
- Data Impact must have exactly one `[x]`
- Do not fabricate implementation steps or control IDs that cannot be inferred — use `TBD`
- Do not add sections not in the template

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I can infer the risk level from context" | Yes — infer it using the risk table; do not ask |
| "The rollback is obvious — just revert the commit" | Write it out explicitly; reviewers and on-call engineers need the exact steps |
| "I'll leave the testing plan section blank" | Every section must be filled; `TBD` is acceptable, blank is not |
| "I need to ask before I can generate" | Generate immediately from context; use `TBD` for anything genuinely unknowable |
| "I can return the spec plus commentary" | One fenced markdown block only |

## Red Flags

- Any checkbox left neither checked nor unchecked
- Risk level set to Low for a schema migration, auth change, or data deletion
- Rollback plan that says only "revert the PR"
- Testing plan with no boxes checked
- Document returned outside a fenced block

## Verification

Before finalizing, confirm:

- [ ] Summary is one clear sentence describing what changes and why
- [ ] Date is filled (deploy date or "TBD")
- [ ] All scope checkboxes are either `[x]` or `[ ]`
- [ ] Risk level has exactly one `[x]` and matches the risk table
- [ ] Data impact has exactly one `[x]`
- [ ] Implementation plan has numbered steps
- [ ] Testing plan has at least one `[x]`
- [ ] Rollback plan has concrete, ordered steps
- [ ] Output is exactly one fenced markdown block
