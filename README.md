# Superpowers (w/ OpenSpec Integration)

> **This is a fork of [obra/superpowers](https://github.com/obra/superpowers).** It adds OpenSpec spec traceability and a DeepFlow project-specific branch.
>
> **Prerequisite:** [`openspec` CLI](https://openspec.dev) must be installed (`npm install -g openspec`) before using the planning workflow.

## Branches

| Branch | Based on | Description |
|--------|----------|-------------|
| `main` | obra/superpowers v5.0.7 | OpenSpec integration — `writing-plans`, `executing-plans`, and `subagent-driven-development` sync specs to OpenSpec. New `openspec` skill for setup and archive. |
| `deepflow` | `main` | All of `main` plus DeepFlow project-specific customizations — Kubernetes test execution, Django architecture context, quality gates, and project workflow skills. |

Install a specific branch:

```bash
# main (OpenSpec only)
git clone -b main https://github.com/seanlin007/superpowers.git ~/superpowers-openspec

# deepflow (OpenSpec + DeepFlow customizations)
git clone -b deepflow https://github.com/seanlin007/superpowers.git ~/superpowers-deepflow
```

---

Superpowers is a complete software development methodology for your coding agents, built on top of a set of composable skills and some initial instructions that make sure your agent uses them.

## How it works

It starts from the moment you fire up your coding agent. As soon as it sees that you're building something, it *doesn't* just jump into trying to write code. Instead, it steps back and asks you what you're really trying to do. 

Once it's teased a spec out of the conversation, it shows it to you in chunks short enough to actually read and digest. 

After you've signed off on the design, your agent puts together an implementation plan that's clear enough for an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing to follow. It emphasizes true red/green TDD, YAGNI (You Aren't Gonna Need It), and DRY. 

Next up, once you say "go", it launches a *subagent-driven-development* process, having agents work through each engineering task, inspecting and reviewing their work, and continuing forward. It's not uncommon for Claude to be able to work autonomously for a couple hours at a time without deviating from the plan you put together.

There's a bunch more to it, but that's the core of the system. And because the skills trigger automatically, you don't need to do anything special. Your coding agent just has Superpowers.


## Sponsorship

If Superpowers has helped you do stuff that makes money and you are so inclined, I'd greatly appreciate it if you'd consider [sponsoring my opensource work](https://github.com/sponsors/obra).

Thanks! 

- Jesse


## Installation

> **This is a fork.** It is not listed on any official marketplace. Install directly from GitHub or a local clone using the instructions below.
>
> **Before installing:** ensure `openspec` is available on your machine:
> ```bash
> npm install -g openspec
> ```

### Claude Code

Clone the repo and install from local path:

```bash
git clone https://github.com/seanlin007/superpowers.git ~/superpowers-openspec
claude plugin install ~/superpowers-openspec --scope user
```

Use `--scope project` instead of `--scope user` to limit the plugin to the current project only (recommended if you have the original `superpowers` installed at user scope — project scope takes priority).

To update:

```bash
cd ~/superpowers-openspec && git pull
```

### GitHub Copilot CLI

Clone the repo and install from local path:

```bash
git clone https://github.com/seanlin007/superpowers.git ~/superpowers-openspec
copilot plugin install ~/superpowers-openspec
```

To update:

```bash
cd ~/superpowers-openspec && git pull
```

### Gemini CLI

Install directly from GitHub:

```bash
gemini extensions install https://github.com/seanlin007/superpowers
```

To update:

```bash
gemini extensions update superpowers
```

### OpenCode

Tell OpenCode:

```
Fetch and follow instructions from https://raw.githubusercontent.com/seanlin007/superpowers/main/.opencode/INSTALL.md
```

### OpenAI Codex CLI

Clone the repo and install from local path:

```bash
git clone https://github.com/seanlin007/superpowers.git ~/superpowers-openspec
```

Open the plugin interface and point it to the local path:

```bash
/plugins
```

Select `Install from local path` and enter `~/superpowers-openspec`.

### Cursor

Clone the repo locally:

```bash
git clone https://github.com/seanlin007/superpowers.git ~/superpowers-openspec
```

In Cursor Agent chat, install from local path:

```text
/add-plugin ~/superpowers-openspec
```

---

### Conflict with original superpowers

Both this fork and the original share the plugin name `superpowers`. **Do not enable both at the same time** — skills from each would conflict. Use one of these strategies:

- **Replace:** uninstall the original and install this fork at user scope.
- **Per-project override:** keep the original at user scope (disabled), install this fork at project scope for projects that need OpenSpec traceability. Project scope takes priority.

## The Basic Workflow

1. **brainstorming** - Activates before writing code. Refines rough ideas through questions, explores alternatives, presents design in sections for validation. Saves design document.

2. **using-git-worktrees** - Activates after design approval. Creates isolated workspace on new branch, runs project setup, verifies clean test baseline.

3. **writing-plans** - Activates with approved design. Breaks work into bite-sized tasks (2-5 minutes each). Every task has exact file paths, complete code, verification steps.

4. **subagent-driven-development** or **executing-plans** - Activates with plan. Dispatches fresh subagent per task with two-stage review (spec compliance, then code quality), or executes in batches with human checkpoints.

5. **test-driven-development** - Activates during implementation. Enforces RED-GREEN-REFACTOR: write failing test, watch it fail, write minimal code, watch it pass, commit. Deletes code written before tests.

6. **requesting-code-review** - Activates between tasks. Reviews against plan, reports issues by severity. Critical issues block progress.

7. **finishing-a-development-branch** - Activates when tasks complete. Verifies tests, presents options (merge/PR/keep/discard), cleans up worktree.

**The agent checks for relevant skills before any task.** Mandatory workflows, not suggestions.

## What's Inside

### Skills Library

**Testing**
- **test-driven-development** - RED-GREEN-REFACTOR cycle (includes testing anti-patterns reference)

**Debugging**
- **systematic-debugging** - 4-phase root cause process (includes root-cause-tracing, defense-in-depth, condition-based-waiting techniques)
- **verification-before-completion** - Ensure it's actually fixed

**Collaboration** 
- **brainstorming** - Socratic design refinement
- **writing-plans** - Detailed implementation plans
- **executing-plans** - Batch execution with checkpoints
- **dispatching-parallel-agents** - Concurrent subagent workflows
- **requesting-code-review** - Pre-review checklist
- **receiving-code-review** - Responding to feedback
- **using-git-worktrees** - Parallel development branches
- **finishing-a-development-branch** - Merge/PR decision workflow
- **subagent-driven-development** - Fast iteration with two-stage review (spec compliance, then code quality)

**Spec Traceability** *(added in this fork)*
- **openspec** - Initialize OpenSpec, create change proposals, and archive completed changes

**Meta**
- **writing-skills** - Create new skills following best practices (includes testing methodology)
- **using-superpowers** - Introduction to the skills system

---

### DeepFlow Branch — Additional Skills

*Available in the `deepflow` branch only.*

**Project Workflow**
- **deepflow-test** - Run pytest suite via `kubeops.sh` with auto-detection of test scope from changed files
- **deepflow-deploy** - Build and deploy to Kubernetes via `kubeops.sh` with interactive target selection
- **deepflow-lint** - Run ruff + prettier via pre-commit on branch-diff files only; auto-fix and report
- **deepflow-pr** - Create PR after all quality gates pass (lint, migrations, tests) with DeepFlow checklist
- **deepflow-issue** - Create GitHub bug or feature issue with DeepFlow standard template

**Design**
- **grill-me** - Relentless interview skill for stress-testing plans and designs

## Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Complexity reduction** - Simplicity as primary goal
- **Evidence over claims** - Verify before declaring success

Read [the original release announcement](https://blog.fsck.com/2025/10/09/superpowers/).

## Contributing

The general contribution process for Superpowers is below. Keep in mind that we don't generally accept contributions of new skills and that any updates to skills must work across all of the coding agents we support.

1. Fork the repository
2. Switch to the 'dev' branch
3. Create a branch for your work
4. Follow the `writing-skills` skill for creating and testing new and modified skills
5. Submit a PR, being sure to fill in the pull request template.

See `skills/writing-skills/SKILL.md` for the complete guide.

## Updating

Superpowers updates are somewhat coding-agent dependent, but are often automatic.

## License

MIT License - see LICENSE file for details

## Community

Superpowers is built by [Jesse Vincent](https://blog.fsck.com) and the rest of the folks at [Prime Radiant](https://primeradiant.com).

- **Discord**: [Join us](https://discord.gg/35wsABTejz) for community support, questions, and sharing what you're building with Superpowers
- **Issues**: https://github.com/obra/superpowers/issues
- **Release announcements**: [Sign up](https://primeradiant.com/superpowers/) to get notified about new versions
