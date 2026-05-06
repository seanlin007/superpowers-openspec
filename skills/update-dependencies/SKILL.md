---
name: update-dependencies
description: Use when updating Python backend dependencies via poetry. Must run inside the deepflow/django:base Docker image — do not run poetry directly on the host.
argument-hint: "[package-name]"
---

# Update Dependencies

Python dependency updates must run inside the `deepflow/django:base` Docker image. Do not run `poetry` directly on the host — the image contains the correct Python environment and `pyconcrete` passphrase setup.

## Step 1: Set Up Worktree

Invoke `superpowers-openspec:using-git-worktrees` to create an isolated branch. Name it `chore/update-<package>`.

## Step 2: Run Poetry Update Inside Docker

```bash
docker run -it --rm -w /app \
  -e PYCONCRETE_PASSPHRASE=$(openssl rand -hex 16) \
  -v ${PWD}/backend:/app \
  --entrypoint /bin/bash \
  deepflow/django:base \
  poetry update {{ARGUMENTS}}
```

If `{{ARGUMENTS}}` is empty, omit the package name to update all dependencies.

After the command completes, `backend/pyproject.toml` and `backend/poetry.lock` will be updated.

## Step 3: Verify No Breaking Changes

```bash
bash kubeops.sh test
```

If tests fail, investigate which package update caused the regression before committing.

## Step 4: Commit

```bash
git add backend/pyproject.toml backend/poetry.lock
git commit -m "chore(deps): update <package> to <version>"
```

## Step 5: Create PR

Invoke `superpowers-openspec:finishing-a-development-branch` Option 2.

PR title format: `chore(deps): update <package>`

## Notes

- Check the changelog / release notes of the updated package before merging if it's a major version bump.
- If updating multiple packages at once, prefer updating one at a time to isolate regressions.
- Frontend dependencies (`frontend/package.json`) are managed with `npm` — this skill covers backend only.
