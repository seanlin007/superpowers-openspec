---
name: test
description: Run the DeepFlow pytest suite on the Kubernetes cluster via kubeops.sh, with auto-detection of relevant test scope from changed files
argument-hint: "[TestClassName or keyword]"
---

# DeepFlow Test Runner

Tests cannot run locally. All pytest execution happens inside a Kubernetes pod with a full environment (PostgreSQL, Redis, MinIO, etc.).

## Steps

1. **Determine test scope**

   If `{{ARGUMENTS}}` is provided, use it as the test keyword directly.

   Otherwise, inspect recently changed files to infer scope:
   - Changed files in `backend/src/mvc/` → target `mvc` tests
   - Changed files in `backend/src/ams/` → target `ams` tests
   - Changed files in `backend/src/workspaces/` → target `workspaces` tests
   - Multiple apps changed → run all tests (no filter)

   Check recent changes with:
   ```bash
   git diff --name-only HEAD
   ```

2. **Run tests**

   With a specific filter:
   ```bash
   option="{{ARGUMENTS}}" bash kubeops.sh test
   ```

   Without a filter (all tests):
   ```bash
   bash kubeops.sh test
   ```

   Run in background — this takes several minutes:
   Use `run_in_background: true`.

3. **Report results**

   When the command completes, report:
   - Pass / fail count
   - Any failed test names and the error summary
   - Link to the full Allure report: `http://pyreport.<EXTERNAL_IP>.nip.io/static/pytest-report-<datetime>/htmlallure/index.html`

4. **Tear down** (ask user first)

   ```bash
   bash kubeops.sh testreset
   ```
