---
name: deepflow-issue
description: Create a GitHub Issue for DeepFlow — bug report or feature request — with the standard template and appropriate labels
argument-hint: "[bug|feature] <title>"
---

# DeepFlow Issue

Creates a GitHub Issue with DeepFlow's standard template.

## Steps

1. **Determine issue type** from `{{ARGUMENTS}}` or ask the user:
   - `bug` — unexpected behaviour, error, regression
   - `feature` — new capability or enhancement

2. **Gather information** before creating:
   - For bugs: reproduction steps, expected vs actual behaviour, relevant logs or error messages, affected Django app (mvc, ams, fms, workspaces, etc.)
   - For features: motivation, proposed approach, affected components

3. **Create issue:**

   **Bug:**
   ```bash
   gh issue create --title "<title>" --label "bug" --body "$(cat <<'EOF'
   ## Description
   <one sentence: what goes wrong>

   ## Steps to Reproduce
   1.
   2.
   3.

   ## Expected Behaviour
   <what should happen>

   ## Actual Behaviour
   <what actually happens>

   ## Affected Component
   - [ ] mvc  - [ ] ams  - [ ] fms  - [ ] workspaces
   - [ ] automl  - [ ] model_garden  - [ ] accounts  - [ ] other: ___

   ## Logs / Error Output
   ```
   <paste relevant logs>
   ```
   EOF
   )"
   ```

   **Feature:**
   ```bash
   gh issue create --title "<title>" --label "enhancement" --body "$(cat <<'EOF'
   ## Motivation
   <why is this needed? what problem does it solve?>

   ## Proposed Approach
   <high-level description of the solution>

   ## Affected Components
   <which Django apps, K8s services, or frontend areas are involved>

   ## Acceptance Criteria
   - [ ]
   - [ ]
   EOF
   )"
   ```

4. **Report issue URL** to the user.

## Notes

- If `{{ARGUMENTS}}` starts with `bug` or `feature`, use that type directly and use the rest as the title.
- Add `--assignee @me` if the user wants to self-assign.
- For bugs discovered during debugging, include the relevant `kubectl logs` output in the Logs section.
