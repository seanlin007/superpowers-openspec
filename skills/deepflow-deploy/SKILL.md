---
name: deploy
description: Build and deploy DeepFlow to the Kubernetes cluster via kubeops.sh, with interactive target selection
argument-hint: "[dev|build|build-core|build-inference|build-develop|push|prepare|purge]"
---

# DeepFlow Deploy

All build and deploy operations go through `bash kubeops.sh`. Do not use `devspace` commands directly.

## Steps

1. **Determine operation**

   If `{{ARGUMENTS}}` is provided, map it to the command below and run it directly.
   Otherwise, ask the user which operation they need:

   | Operation | Command |
   |-----------|---------|
   | Full dev environment (Kind + deploy) | `bash kubeops.sh dev` |
   | Build all images | `bash kubeops.sh build` |
   | Build single image | `bash kubeops.sh build <target>` |
   | Push images to registry | `bash kubeops.sh push` |
   | Prepare infra only | `bash kubeops.sh prepare-infra` |
   | Prepare app only | `bash kubeops.sh prepare-dev` |
   | Tear down app | `bash kubeops.sh purge-dev` |
   | Tear down infra | `bash kubeops.sh purge-infra` |
   | Reset dev environment | `bash kubeops.sh devreset` |

   Single-image build targets: `build-base`, `build-core`, `build-inference`, `build-analysis`, `build-predictor`, `build-develop`, `build-extension`

2. **Execute**

   Run the selected command. Use `run_in_background: true` for `build`, `dev`, and `push` operations as they take several minutes.

3. **Verify**

   After deploy completes, confirm the relevant pods are running:
   ```bash
   kubectl -n ${NAMESPACE} get pods
   ```

   Report any pods not in `Running` state.
