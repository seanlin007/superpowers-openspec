---
name: finishing-a-development-branch
description: Use when implementation is complete, all tests pass, and you need to decide how to integrate the work - guides completion of development work by presenting structured options for merge, PR, or cleanup
---

# Finishing a Development Branch

## Overview

Guide completion of development work by presenting clear options and handling chosen workflow.

**Core principle:** Verify tests → Present options → Execute choice → Clean up.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this work."

## The Process

### Step 1: Verify Tests and Quality Gates

**REQUIRED:** Use `superpowers-openspec:verification-before-completion` — the Iron Law applies here. Do not present options until all checks below pass and evidence is in hand.

**1a. Clean working tree — no uncommitted changes allowed:**
```bash
git status
```
If there are uncommitted or untracked files, stop. Commit or discard them before continuing.

**Before presenting options, run all three checks in order:**

**1b. Detect upstream branch:**
```bash
UPSTREAM=$(git rev-parse --abbrev-ref @{u} 2>/dev/null || echo "origin/main")
BASE=$(git merge-base HEAD $UPSTREAM)
echo "Upstream: $UPSTREAM  Base: $BASE"
```
Use `$BASE` in the lint command below, and `$UPSTREAM` as the base branch in Step 2.

**1c. Lint:**
```bash
pre-commit run --from-ref $BASE --to-ref HEAD
```
Fix any ruff or prettier failures before continuing.

**1d. Migration check (git-based — Django runs in K8s, cannot run locally):**
```bash
git diff --name-only $BASE HEAD | grep 'models\.py'
```
If any `models.py` files appear in the output, check that a corresponding new migration file exists in the same diff:
```bash
git diff --name-only $BASE HEAD | grep 'migrations/'
```
If model files changed but no migration files appear, stop and create the migration inside the K8s pod before continuing.

**1e. Test suite** (runs in Kubernetes — takes several minutes):
```bash
bash kubeops.sh test
```
Use `run_in_background: true`.

**If any check fails:**
```
Cannot proceed — <lint|migration|test> check failed:

[Show failures]

Fix before completing.
```

Stop. Don't proceed to Step 2.

**If all pass:** Continue to Step 2.

### Step 2: Determine Base Branch

Use `$UPSTREAM` detected in Step 1b as the base branch. If upstream was not set, ask:

> "Which branch did this branch split from? (e.g., main, develop, release/x.y)"

### Step 3: Present Options

Present exactly these 4 options:

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

**Don't add explanation** - keep options concise.

### Step 4: Execute Choice

#### Option 1: Merge Locally

```bash
# Switch to base branch
git checkout <base-branch>

# Pull latest
git pull

# Merge feature branch
git merge <feature-branch>

# Verify tests on merged result
<test command>

# If tests pass
git branch -d <feature-branch>
```

Then: Cleanup worktree (Step 5)

#### Option 2: Push and Create PR

Invoke `superpowers-openspec:deepflow-pr` — it handles upstream detection, quality gates, and PR creation with the standard checklist.

Then: Cleanup worktree (Step 5)

> **After PR is created:** When reviewer feedback arrives, use `superpowers-openspec:receiving-code-review` to evaluate and respond. Do not implement suggestions without verification.
>
> **After PR is merged:** clean up the worktree:
> ```bash
> git worktree remove <worktree-path>
> git branch -d <feature-branch>
> ```

#### Option 3: Keep As-Is

Report: "Keeping branch <name>. Worktree preserved at <path>."

**Don't cleanup worktree.**

#### Option 4: Discard

**Confirm first:**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

Wait for exact confirmation.

If confirmed:
```bash
git checkout <base-branch>
git branch -D <feature-branch>
```

Then: Cleanup worktree (Step 5)

### Step 5: Cleanup Worktree

**For Options 1, 2, 4:**

Check if in worktree:
```bash
git worktree list | grep $(git branch --show-current)
```

If yes:
```bash
git worktree remove <worktree-path>
```

**For Option 3:** Keep worktree.

## Quick Reference

| Option | Merge | Push | Keep Worktree | Cleanup Branch |
|--------|-------|------|---------------|----------------|
| 1. Merge locally | ✓ | - | - | ✓ |
| 2. Create PR | - | ✓ | ✓ | - |
| 3. Keep as-is | - | - | ✓ | - |
| 4. Discard | - | - | - | ✓ (force) |

## Common Mistakes

**Skipping quality gate verification**
- **Problem:** Merge broken code, create failing PR with lint errors or missing migrations
- **Fix:** Always run pre-commit (from upstream base), migration diff check, and kubeops.sh test before offering options

**Open-ended questions**
- **Problem:** "What should I do next?" → ambiguous
- **Fix:** Present exactly 4 structured options

**Automatic worktree cleanup**
- **Problem:** Remove worktree when might need it (Option 2, 3)
- **Fix:** Only cleanup for Options 1 and 4

**No confirmation for discard**
- **Problem:** Accidentally delete work
- **Fix:** Require typed "discard" confirmation

## Red Flags

**Never:**
- Proceed with failing tests
- Merge without verifying tests on result
- Delete work without confirmation
- Force-push without explicit request

**Always:**
- Verify tests before offering options
- Present exactly 4 options
- Get typed confirmation for Option 4
- Clean up worktree for Options 1 & 4 only

## Integration

**Called by:**
- **subagent-driven-development** (Step 7) - After all tasks complete
- **executing-plans** (Step 5) - After all batches complete

**Pairs with:**
- **using-git-worktrees** - Cleans up worktree created by that skill
