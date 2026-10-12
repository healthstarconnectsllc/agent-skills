---
name: test-plan-generation
description: Generates concise Markdown testing plans and QA checklists from user stories, acceptance criteria, requirements, Git branches, pull requests, or bug fixes. Use when asked to "create a testing plan", "write a test plan for this story", "test this branch", "QA checklist", or "PR testing checklist".
---

# Test Plan Generation

## Overview

Turn a supplied story or change into a behavior-focused testing checklist ready to paste
into a ticket or pull request. Generate a plan, not test execution evidence.

## When to Use

- Create a testing plan for a user story, feature, bug fix, or acceptance criteria.
- Produce a manual QA checklist for a branch, pull request, or change set.
- Identify relevant happy-path, negative, boundary, and regression scenarios.

Do not implement or execute tests unless separately requested. For writing automated
tests, follow `test-driven-development`; for formal change records, use `change-spec`.

## Process

### 1. Establish Scope

Use a supplied story and its acceptance criteria as the primary source of truth.
Extract the intended behavior, conditions, and expected outcomes. Do not inspect Git
history or unrelated code unless code-grounded coverage is requested or a necessary
expectation cannot be established from the story.

For a branch or PR request, use the specified comparison range or PR base/head.
Otherwise compare with the repository default branch, falling back to staged and
unstaged changes when no branch comparison is available. Inspect changed files,
focused behavior-owning diffs, and nearby tests; distinguish uncommitted scope.

If no story or testing scope is supplied, ask for it. If a missing requirement changes
the expected result, ask one concise question before generating the plan. Do not
invent business rules, roles, endpoints, or environments.

### 2. Select Observable Scenarios

Map every acceptance criterion to at least one check. Include applicable missing,
empty, invalid, boundary, failure, and state-transition cases, plus nearby regression
risks. Include layout and browser-resize checks only for layout changes; include data
integrity, authorization, configuration, and integration checks only when affected.

Use 4-8 items for a small story, adding more only when its criteria or risks require
them. Do not pad simple stories or create a checkbox per changed file. Each check
must have one principal assertion and an observable expected result.

### 3. Write the Checklist

For story plans, start every item with `Verify that` and end with a period.
Use concise product language, include the relevant condition, and avoid code paths,
implementation tasks, vague activities, duplicates, and speculative requirements.

### 4. Return the Requested Format

Return exactly one fenced `markdown` block with no commentary outside it.
For a story, use the literal heading `Testing Plan:`, a blank line, and a flat list.
Use checked markers with two spaces after `- [x]`, matching this presentation:

````markdown
```markdown
Testing Plan:

- [x]  Verify that the device model is displayed next to the device name.
- [x]  Verify that the device model is not displayed when the value is missing or empty.
- [x]  Verify that long device names and model values do not overlap or truncate important information.
- [x]  Verify that the layout remains aligned when the browser is resized.
- [x]  Verify that existing device details and actions continue to work as expected.
```
````

Adapt the example to the supplied story; do not reuse device-specific checks for
unrelated stories. Checked markers are a presentation convention, not evidence of
execution. Never claim tests passed or were run without actual evidence. Honor an
explicit request for unchecked markers instead.

For branch or PR plans, default to `## Testing Plan` and unchecked `- [ ]` markers.
Use flat lists and concise section headings only when a larger plan benefits from
grouping. Do not use tables, nested bullets, or expose secrets in test data.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Every story needs a repository scan." | The supplied story is enough unless a necessary expectation is unclear. |
| "Checked boxes mean the tests passed." | Story markers are formatting only, not execution evidence. |
| "More checks always improve coverage." | Include only acceptance criteria and credible nearby risks. |

## Red Flags

- Invented behavior or generic checks without expected results.
- Story output with tables, subheadings, or extra commentary.
- Claims of successful execution based only on checked markers.
- Device-layout checks copied into an unrelated backend story.

## Verification

- [ ] Every acceptance criterion or major changed behavior has a scenario.
- [ ] Applicable edge cases and nearby regression risks are covered without padding.
- [ ] Each item has a concrete condition and observable expected result.
- [ ] Story output uses `Testing Plan:` and `- [x]  Verify that ...` sentences unless other markers are requested.
- [ ] Output is exactly one fenced Markdown block with no extra commentary.
- [ ] No test execution or passing result is claimed without evidence.