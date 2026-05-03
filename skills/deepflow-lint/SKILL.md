---
name: deepflow-lint
description: Run DeepFlow linting checks — ruff and prettier via pre-commit, with clear pass/fail reporting
---

# DeepFlow Lint

Runs the full pre-commit hook suite (ruff + prettier) and reports results.

## Steps

1. **Run all hooks:**

   ```bash
   pre-commit run --from-ref origin/main --to-ref HEAD
   ```

2. **Report results:**

   - If all hooks pass: "Lint clean — no issues."
   - If any hook fails: list each failed file and the error message from ruff or prettier.

3. **Fix failures:**

   Ruff auto-fix (safe fixes only):
   ```bash
   ruff check --fix backend/src/
   ```

   Prettier auto-fix (YAML files):
   ```bash
   prettier --write "**/*.yaml"
   ```

   Re-run `pre-commit run --from-ref origin/main --to-ref HEAD` to confirm clean.

## Notes

- Only files changed in this branch (vs `origin/main`) are checked — not the entire codebase.
- ruff enforces line-length 100 and LF line endings. Do not use `autopep8` or `black`.
- Hooks cover `backend/src/**` and `backend/fixtures/**`. Migrations and static files are excluded.
- If lint failures are in pre-existing code not related to your change, you may skip with `git commit --no-verify`. Use sparingly and note it in the PR description.
