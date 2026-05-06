---
name: hotfix
description: Use when a bug needs an urgent fix that bypasses the normal brainstorm → plan → execute flow. Covers diagnosis, TDD fix, verification, and PR creation without full planning overhead.
---

# Hotfix

Fast-path for urgent bug fixes. Skips brainstorming and writing-plans. Goes straight to diagnosis → fix → verify → PR.

**Announce at start:** "I'm using the hotfix skill to address this bug."

<HARD-GATE>
This skill is for genuine bugs with a known symptom. Do NOT use it for:
- New features or enhancements (use brainstorming)
- Refactoring (use brainstorming)
- Anything that requires design decisions (use brainstorming)

If you are unsure whether this qualifies as a hotfix, default to brainstorming.
</HARD-GATE>

## Step 1: Set Up Worktree

Invoke `superpowers-openspec:using-git-worktrees` to create an isolated branch before touching any code. Name the branch `hotfix/<short-description>`.

## Step 2: Diagnose

Invoke `superpowers-openspec:systematic-debugging` to establish root cause before writing any fix. Do not proceed until the root cause is confirmed with evidence.

## Step 3: Fix with TDD

Invoke `superpowers-openspec:test-driven-development`:
1. Write a failing test that reproduces the bug
2. Verify it fails (RED)
3. Write the minimal fix
4. Verify it passes (GREEN)
5. Commit: `fix(<scope>): <description>`

## Step 4: Verify

Invoke `superpowers-openspec:verification-before-completion`:

```bash
# Lint
UPSTREAM=$(git rev-parse --abbrev-ref @{u} 2>/dev/null || echo "origin/main")
BASE=$(git merge-base HEAD $UPSTREAM)
pre-commit run --from-ref $BASE --to-ref HEAD

# Migration check
git diff --name-only $BASE HEAD | grep 'models\.py'
git diff --name-only $BASE HEAD | grep 'migrations/'

# Tests
bash kubeops.sh test
```

Do not proceed if any check fails.

## Step 5: Create PR

Invoke `superpowers-openspec:deepflow-pr` or use `superpowers-openspec:finishing-a-development-branch` Option 2 directly.

PR title format: `fix(<scope>): <description>`

## Red Flags

**Stop and switch to brainstorming if:**
- The fix requires touching more than 2-3 files beyond the bug site
- You discover the bug is a symptom of a deeper design problem
- The fix requires new configuration, migrations beyond the bugfix, or API changes
