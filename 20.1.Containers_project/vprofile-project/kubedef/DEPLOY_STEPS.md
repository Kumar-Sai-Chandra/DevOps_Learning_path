Deployment checklist & fixes for vprofile-project (kubedef)

Overview
- This file collects the dry-run findings and actionable fixes required to deploy the manifests under `kubedef`.

Key issues discovered
1. apiVersion typos
   - Replace any `apiversion` or incorrect values with correct capitalization and values:
     - Deployments: `apiVersion: apps/v1`
     - Services/Secrets/PVC: `apiVersion: v1`
     - Ingress: `apiVersion: networking.k8s.io/v1`

2. YAML syntax and indentation problems
   - `dbdeploy.yml` has malformed `ports` / `containerPort` entries — ensure `ports:` contains mapped objects with `containerPort: <number>`.
   - `appdeploy.yml` `initContainers:` section is mis-indented and concatenated with comments; cleanly place `initContainers` under `spec.template.spec`.

3. Ingress host and backend mismatch
   - `ingress/ingress.yml` uses host `vprofile.hkhinfotech.xyz` (external). If you don't have this DNS, change to a local host (e.g., `vpro.local`) and/or use `/etc/hosts` mapping.
   - Ingress backend references `vprofile-service` but your app Service file declares `vproapp-service`. Either change the Ingress backend name to `vproapp-service` or rename the Service to match.

4. Missing secret keys
   - `rmqdeploy.yml` references `rabbitmq-password` in Secret `mysecret`, but `secrets/secrets.yml` only contains `db-password`. Add `rabbitmq-password` (base64 encoded) to `secrets.yml`.

5. Images and image pulling
   - Ensure `kumarsaichandra37/myfirst_devops:app` and `:db` images exist on Docker Hub or load the local images into your cluster (Minikube/kind) or set appropriate `imagePullPolicy`.

6. PVC and storage
   - `volumes/dbpvc.yml` uses `apiversion` typo; also verify your cluster has a default StorageClass. For local clusters, consider a hostPath/local PV for dev.

7. Misc
   - Add `livenessProbe` and `readinessProbe` to `vproapp` later.

Minimal fix steps (apply in repo files)
1. Fix apiVersion capitalization across all YAMLs.
2. Correct YAML indentation/port mappings in `dbdeploy.yml` and `appdeploy.yml` init containers.
3. Edit `ingress/ingress.yml`:
   - Set `host` to a local host (e.g., `vpro.local`) or remove host if you will use NodePort.
   - Set backend `service.name` to `vproapp-service` (or rename Service).
4. Add `rabbitmq-password` to `kubedef/secrets/secrets.yml` (base64 of chosen password).
5. Confirm images exist or provide instructions to build/load/push:
   - Minikube example:
     ```bash
     eval $(minikube -p minikube docker-env)
     docker build -t kumarsaichandra37/myfirst_devops:app ./20.1.Containers_project/vprofile-project
     docker build -t kumarsaichandra37/myfirst_devops:db ./20.1.Containers_project/vprofile-project
     ```
   - kind example:
     ```bash
     docker build -t kumarsaichandra37/myfirst_devops:app .
     kind load docker-image kumarsaichandra37/myfirst_devops:app
     ```
6. Ensure PVC binds: either rely on StorageClass or create a dev PV (hostPath) if required.

Validation commands (dry-run)
- Client-side validation (no cluster required):
  ```bash
  kubectl apply --dry-run=client -f 20.1.Containers_project/vprofile-project/kubedef/deployments/appdeploy.yml
  kubectl apply --dry-run=client -f 20.1.Containers_project/vprofile-project/kubedef/service/appservice.yml
  kubectl apply --dry-run=client -f 20.1.Containers_project/vprofile-project/kubedef/ingress/ingress.yml
  ```
- Server-side dry-run (if you have cluster access):
  ```bash
  kubectl apply --dry-run=server -f <file>.yml
  ```

Access options without DNS
- NodePort: expose app via `--type=NodePort` and use `http://<NODE_IP>:<NODE_PORT>`.
- LoadBalancer: cloud or MetalLB on bare metal (requires MetalLB IPPool configuration).
- Ingress + /etc/hosts: map chosen host (e.g., `vpro.local`) to Ingress IP.
- Port-forward: `kubectl port-forward svc/vproapp-service 8080:8080` for quick local testing.

Next actions I can take for you (pick any)
- A) Apply automatic fixes to YAMLs (apiVersion fixes, ingress service name, add rabbitmq placeholder) and run `kubectl apply --dry-run=client`.
- B) Provide a simple `hostPath` PV manifest for local PVC binding.
- C) Create a `.gitignore` and remove large files (if you want cleanup).

File location
- Saved: `20.1.Containers_project/vprofile-project/kubedef/DEPLOY_STEPS.md`

If you want A or B applied now, tell me which environment to assume (Minikube / Docker Desktop / kind / cloud).