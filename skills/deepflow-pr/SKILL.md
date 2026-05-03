---
name: deepflow-pr
description: Create a Pull Request for DeepFlow with the standard checklist — migrations, API changes, K8s manifest changes, and test evidence
argument-hint: "[PR title]"
---

# DeepFlow Pull Request

Creates a GitHub PR with DeepFlow's standard checklist, after verifying all quality gates pass.

## Steps

1. **Run quality gates** (all three must pass before creating PR):

   ```bash
   pre-commit run --all-files
   python manage.py makemigrations --check
   bash kubeops.sh test
   ```

   Do not proceed if any fails.

2. **Determine base branch:**

   ```bash
   git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
   ```

3. **Summarize changes** — inspect commits and diff:

   ```bash
   git log <base>..HEAD --oneline
   git diff <base>..HEAD --stat
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
   - [ ] `pre-commit run --all-files` clean
   - [ ] `python manage.py makemigrations --check` exits 0
   EOF
   )"
   ```

6. **Report PR URL** to the user.

## Notes

- Assign reviewers if known: add `--reviewer <github-username>` to the `gh pr create` command.
- If `{{ARGUMENTS}}` is provided, use it as the PR title directly.
- Never create a PR with failing tests or pending migrations.
