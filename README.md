# SonarQube GitOps Deployment

Deploys SonarQube (Community Edition) on Kubernetes using GitOps with ArgoCD.

## Stack
- **SonarQube** 10.4 Community
- **PostgreSQL** 15 (StatefulSet)
- **ArgoCD** for GitOps CD
- **GitHub Actions** for CI (manifest validation)

## Before You Deploy

### Update your GitHub repo URL
Edit `argocd/application.yaml`:
```yaml
repoURL: https://github.com/YOUR_ORG/sonarqube-gitops
```

## Deployment Steps

```bash
# 1. Push code to GitHub first, then:

# 2. Apply ArgoCD Application
kubectl apply -f argocd/application.yaml

# 3. Watch pods come up (takes ~2-3 minutes)
kubectl get pods -n sonarqube -w

# 4. Get the external IP once pods are Running
kubectl get svc sonarqube -n sonarqube
# Look for EXTERNAL-IP column → access via http://<EXTERNAL-IP>:9000
```

## Default Login
- URL: `http://<EXTERNAL-IP>:9000`
- Username: `admin`
- Password: `admin`  ← change this after first login!

## Files Overview
| File | Purpose |
|------|---------|
| k8s/namespace.yaml | Creates `sonarqube` namespace |
| k8s/secret.yaml | PostgreSQL credentials |
| k8s/postgres-statefulset.yaml | PostgreSQL database |
| k8s/postgres-service.yaml | Internal DB service |
| k8s/sonarqube-pvc.yaml | Persistent storage for SonarQube |
| k8s/sonarqube-deployment.yaml | SonarQube app |
| k8s/sonarqube-service.yaml | LoadBalancer service (external IP) |
| argocd/application.yaml | ArgoCD app definition |

## ⚠️ Notes
- SonarQube needs `vm.max_map_count=524288` — handled by init container
- First startup takes 2-3 minutes, be patient
- Requires minimum 2GB RAM on the node
- GCP will provision an external IP automatically via LoadBalancer
