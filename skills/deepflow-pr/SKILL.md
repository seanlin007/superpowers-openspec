---
name: deepflow-pr
description: Create a Pull Request for DeepFlow with the standard checklist — migrations, API changes, K8s manifest changes, and test evidence
argument-hint: "[PR title]"
---

# DeepFlow Pull Request

Creates a GitHub PR with DeepFlow's standard checklist, after verifying all quality gates pass.

## Steps

1. **Detect upstream branch and run quality gates** (all must pass before creating PR):

   ```bash
   UPSTREAM=$(git rev-parse --abbrev-ref @{u} 2>/dev/null || echo "origin/main")
   BASE=$(git merge-base HEAD $UPSTREAM)
   ```

   Then run in order:

   ```bash
   # Lint
   pre-commit run --from-ref $BASE --to-ref HEAD

   # Migration check (git-based — Django runs in K8s, cannot run locally)
   git diff --name-only $BASE HEAD | grep 'models\.py'
   git diff --name-only $BASE HEAD | grep 'migrations/'
   ```
   If model files changed but no migration files appear, stop and create the migration inside the K8s pod first.

   ```bash
   # Tests
   bash kubeops.sh test
   ```

   Do not proceed if any fails.

2. **Summarize changes** — inspect commits and diff:

   ```bash
   git log $BASE..HEAD --oneline
   git diff $BASE..HEAD --stat
   ```

4. **Push branch:**

   ```bash
   git push -u origin <feature-branch>
   ```

5. **Create PR:**

   ```bash
   gh pr create --title "<title>" --body "$(cat <<'EOF'
   ## Summary
   <2-3 bullets of what changed and why>

   ## Migrations
   - [ ] No new migrations
   - [ ] New migrations included and reviewed

   ## API Changes
   - [ ] No API changes
   - [ ] API changes are backwards-compatible
   - [ ] Breaking API changes — documented and coordinated with consumers

   ## Kubernetes / Config Changes
   - [ ] No manifest or config changes
   - [ ] Changes to Helm values / K8s manifests included

   ## Test Evidence
   - [ ] `bash kubeops.sh test` passes
   - [ ] `pre-commit run --from-ref <base> --to-ref HEAD` clean
   - [ ] No missing migrations (models.py changed → migration file present in diff)
   EOF
   )"
   ```

6. **Report PR URL** to the user.

## Notes

- Assign reviewers if known: add `--reviewer <github-username>` to the `gh pr create` command.
- If `{{ARGUMENTS}}` is provided, use it as the PR title directly.
- Never create a PR with failing tests or pending migrations.
