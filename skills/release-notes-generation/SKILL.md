---
name: release-notes-generation
description: Generates plain-English, copyable release notes from a GitHub release pull request and the issues it closes. Use when asked to "generate release notes", "write release notes for PR <n>", "release notes for release/x.y.z", or to summarize a release PR for a non-technical audience.
---

# Release Notes Generation

## Overview

Turn a release pull request and its `Closes #N` issues into short release notes a
non-technical reader can follow. Output one bullet per ticket plus a brief closing
summary, ready to paste into a chat or ticket.

## When to Use

- A release PR (for example `release/1.13.0` into `main`) needs release notes.
- A list of closed issues needs a non-technical summary.

Do not use this for changelogs aimed at developers, commit-level histories, or
formal change records (use `change-spec`).

## Process

### 1. Fetch the Release PR

Fetch the PR from the repository it was given in. Prefer the `gh api` REST endpoints
(`repos/{owner}/{repo}/pulls/{n}`) when GraphQL is rate limited. Take the version from
the PR title or head branch (`release/1.12.4` -> `1.12.4`). If neither contains a
version, ask for it.

### 2. Collect the Tickets

Parse every `Closes #N`, `Fixes #N`, and `Resolves #N` reference in the PR body, in the
order they appear, ignoring duplicates. Fetch each issue's title, labels, and body from
the PR's own repository only; never resolve a number against another repository. If an
issue cannot be fetched, keep its bullet, say so under "Please confirm", and do not
guess its content.

### 3. Write One Bullet per Ticket

- Use exactly one plain-English sentence per ticket, in PR-body order.
- Choose the prefix from labels and title: `FE:`, `BE:`, or `FE/BE:`. For bugs, put
  `BugFix` before it (`BugFix BE:`). If labels and title disagree or are missing, pick
  the best fit and list it under "Please confirm".
- Describe the outcome or benefit, not the implementation. Avoid jargon, class names,
  and library internals unless the tool name is the whole point (for example "Okta").
- Append `No user impact` for purely internal work: dependency upgrades, refactors,
  cleanup, test or build changes, and groundwork for future features.
- Link the number as `[#n](https://github.com/<owner>/<repo>/issues/<n>)`.
- Do not invent behavior. If a ticket is too technical or ambiguous to describe
  confidently, write the best reading and add it to "Please confirm".

### 4. Write the Closing Summary

After a blank line, address the recipient as `@<recipient> ,`, using the name the user
gives. If no recipient is given, ask for one. Write two sentences for a non-technical audience:
user-visible changes first, then security or framework work "behind the scenes". End
with exactly "If you notice any issues in these areas, please let us know."

### 5. Return the Output

Use today's date in the heading, written like `Oct 29, 2026`. Return the notes as one
raw fenced `markdown` block so they can be copied. After the block, add a short
"Please confirm" list only when something was ambiguous; otherwise omit it.

````markdown
```markdown
## Release Notes 1.12.4 (Oct 29, 2026)

- BugFix FE: Fixed the device list so it no longer shows duplicate entries after a search. [#101](https://github.com/<owner>/<repo>/issues/101)
- BE: Updated supporting software to its latest versions to stay secure. No user impact [#102](https://github.com/<owner>/<repo>/issues/102)

@<recipient> , This release fixes duplicate entries in the device list. Behind the scenes, we updated supporting software for security and stability. If you notice any issues in these areas, please let us know.
```
````

Adapt the example to the actual tickets; never reuse its content for another release.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Reorder bullets by importance." | Keep the order of the PR body. |
| "Mention the library names, they're precise." | The audience is non-technical; describe the outcome. |
| "The title is clear enough, skip the body." | Titles are often technical; read labels and body before writing. |
| "Fill in details the ticket doesn't state." | Do not invent behavior; flag it under "Please confirm". |

## Red Flags

- Bullets containing class names, version numbers, or implementation steps.
- Issue links pointing at the wrong repository or without hyperlinks.
- Internal-only work without `No user impact`.
- More than one sentence per ticket, or a summary longer than two sentences.
- Output that is not a single raw markdown block.

## Verification

- [ ] The version matches the PR title or head branch.
- [ ] Every `Closes/Fixes/Resolves` reference has exactly one bullet, in PR-body order.
- [ ] Each bullet has a correct prefix, one plain-English sentence, and a working issue hyperlink.
- [ ] Internal-only tickets end with `No user impact`.
- [ ] The summary is two sentences and ends with the required closing line.
- [ ] Ambiguous tickets are listed under "Please confirm" after the code block.
