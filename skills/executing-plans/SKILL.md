---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute all tasks, report when complete.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

**Note:** Tell your human partner that Superpowers works much better with access to subagents. The quality of its work will be significantly higher if run on a platform with subagent support (such as Claude Code or Codex). If subagents are available, use superpowers-openspec:subagent-driven-development instead of this skill.

## The Process

**REQUIRED before starting:** Confirm you are on a feature branch in a worktree, not on main. If no worktree exists, invoke `superpowers-openspec:using-git-worktrees` first.

### Step 1: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Tasks

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. **REQUIRED:** Use `superpowers-openspec:verification-before-completion` before marking as completed
5. Mark as completed

### Step 3: Archive OpenSpec Change

After all tasks are complete and verified, archive the OpenSpec change to preserve the spec:

1. Read the `**OpenSpec Change:**` line from the plan header to get `<change-name>`. If none, skip this step.
2. Decide whether the completed work changes user-facing behavior, contracts, or capabilities that should be reflected in top-level specs.
3. Use `--skip-specs` only for tooling, infrastructure, tests, docs, refactors, or other implementation-only changes. File paths can help you inspect the diff, but they are hints, not the decision rule.
4. If you are unsure, omit `--skip-specs` and archive with the spec update.

**User-facing change:**
```bash
openspec archive <change-name>
git add .openspec/
git commit -m "chore: archive openspec change <change-name> with spec update"
```

**Internal/infrastructure change:**
```bash
openspec archive <change-name> --skip-specs
git add .openspec/
git commit -m "chore: archive openspec change <change-name>"
```

### Step 4: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers-openspec:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly

**When blocked by a bug or test failure:** Use `superpowers-openspec:systematic-debugging` before asking for help or guessing. Only escalate to the human if debugging confirms the plan itself is wrong.

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Remember
- Review plan critically first
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Stop when blocked, don't guess
- Never start implementation on main/master branch without explicit user consent

## Integration

**Required workflow skills:**
- **superpowers-openspec:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers-openspec:writing-plans** - Creates the plan this skill executes
- **superpowers-openspec:finishing-a-development-branch** - Complete development after all tasks
