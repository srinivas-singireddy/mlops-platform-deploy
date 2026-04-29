# mlops-platform-deploy

Application manifests for the **mlops-platform-aws-eks** EKS cluster.

This repo follows the **two-repo GitOps pattern**: infrastructure lives in
[mlops-platform-aws-eks](https://github.com/srinivas-singireddy/mlops-platform-aws-eks),
this repo holds Kubernetes manifests that ArgoCD watches and reconciles
to the cluster.

## Layout

\`\`\`
apps/
├── kube-prometheus-stack/   # Prometheus + Grafana + Alertmanager
├── loki/                    # Log aggregation
└── promtail/                # Log shipper (DaemonSet)
\`\`\`

Each subfolder contains an ArgoCD `Application` resource and any Helm values.

## How it works

1. The "root" ArgoCD Application lives in mlops-platform-aws-eks at
   `k8s/bootstrap/root-app.yaml`. It points at this repo's `apps/` folder.
2. ArgoCD discovers all Applications in that folder and creates them.
3. Each child Application points at a specific subfolder (Helm chart values).
4. Push to this repo → ArgoCD detects the change → cluster reconciles.

## Adding a new app

1. Create a subfolder under `apps/`
2. Add `application.yaml` (the ArgoCD Application resource)
3. Add `values.yaml` if it's a Helm-based app
4. Commit and push
5. ArgoCD picks it up within 3 minutes (default sync interval)
