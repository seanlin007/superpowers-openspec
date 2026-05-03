---
name: deepflow-lint
description: Run DeepFlow linting checks — ruff and prettier via pre-commit, with clear pass/fail reporting
---

# DeepFlow Lint

Runs the full pre-commit hook suite (ruff + prettier) and reports results.

## Steps

1. **Run all hooks:**

   ```bash
   pre-commit run --all-files
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

   Re-run `pre-commit run --all-files` to confirm clean.

## Notes

- ruff enforces line-length 100 and LF line endings. Do not use `autopep8` or `black`.
- Hooks cover `backend/src/**` and `backend/fixtures/**`. Migrations and static files are excluded.
- Run lint before every commit and before creating a PR.
