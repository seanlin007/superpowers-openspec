---
name: openspec
description: Use to verify OpenSpec is installed and initialize the repo — called by writing-plans before writing begins. Also documents how to create change proposals and archive changes.
---

# OpenSpec Integration

Manages spec-driven development traceability using the OpenSpec CLI.

## Step 1: Verify OpenSpec is installed

```bash
openspec --version
```

If not found, stop and install it:

```bash
npm install -g openspec
```

Confirm installation succeeded before continuing.

## Step 2: Initialize if needed

Check whether `.openspec/` exists in the project root. If it does not:

```bash
openspec init --tools none
git add .openspec/
git commit -m "chore: initialize openspec for spec traceability"
```

This only needs to run once per repository. After these two steps, return control to the calling skill.

## Create a Change Proposal

When starting a new feature or implementation plan:

```bash
openspec new change <feature-name> --description "<one-sentence goal>"
```

`<feature-name>` should match the plan filename slug (e.g., `shareskill-mvp`).

After creating, record the change name at the top of the implementation plan file, inside the header block:

```markdown
**OpenSpec Change:** `<feature-name>`
```

## Check Artifact Status

```bash
openspec status --change <feature-name>
```

Shows which artifacts (proposal, specs, design, tasks) have been filled in.

## Archive a Completed Change

When all tasks are done, the branch is reviewed, and it is ready to merge:

1. Decide whether the completed work changes user-facing behavior, contracts, or capabilities that should be reflected in top-level specs.
2. Use `--skip-specs` only for tooling, infrastructure, tests, docs, refactors, or other implementation-only changes.
3. File paths can help you inspect the diff, but they are hints, not the decision rule.
4. If you are unsure, omit `--skip-specs` and archive with the spec update.

**Implementation-only change:**

```bash
openspec archive <feature-name> --skip-specs
```

**Spec-affecting or user-facing change:**

```bash
openspec archive <feature-name>
```

Commit the archive result:

```bash
git add .openspec/
git commit -m "chore: archive openspec change <feature-name>"
```
