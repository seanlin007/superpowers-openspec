---
name: openspec
description: Use to initialize OpenSpec, create change proposals, and archive completed changes for spec traceability
---

# OpenSpec Integration

Manages spec-driven development traceability using the OpenSpec CLI. This skill is used by other skills (writing-plans, executing-plans, subagent-driven-development) to keep specs alive after implementation.

## Prerequisite Check

**Always verify OpenSpec is installed before any operation:**

```bash
openspec --version
```

If not found:

```bash
npm install -g openspec
```

Confirm installation succeeded before continuing.

## Initialization

Check whether `.openspec/` exists in the project root. If it does not:

```bash
openspec init --tools none
git add .openspec/
git commit -m "chore: initialize openspec for spec traceability"
```

This only needs to run once per repository.

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

```bash
openspec archive <feature-name> --skip-specs
```

Use `--skip-specs` for tooling, infrastructure, or implementation-only changes where updating top-level specs is not needed. Omit `--skip-specs` when the change introduces or modifies user-facing specs that should be reflected in the main spec files.

Commit the archive result:

```bash
git add .openspec/
git commit -m "chore: archive openspec change <feature-name>"
```
