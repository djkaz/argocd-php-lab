# 🚀 PHP GitOps Project: ArgoCD & Helm

This repository demonstrates a complete **GitOps** workflow. It deploys a multi-page PHP application into a Kubernetes cluster using **ArgoCD** for continuous delivery and **Helm** for resource orchestration.



## 🏗️ Architecture
The application uses a **Sidecar Pattern** to ensure high performance:
* **Nginx Container:** Handles HTTP requests (Port 80).
* **PHP-FPM Container:** Processes PHP logic (Port 9000).
* **ConfigMaps:** Serve as a "Virtual Disk" for source code and Nginx config.



---

## 📁 Project Structure
```text
.
├── charts/
│   └── php-stack/          # Helm Chart
│       ├── Chart.yaml      # Metadata
│       └── templates/      # K8s Manifests
│           ├── configmap-code.yaml   # PHP Website Files
│           ├── configmap-nginx.yaml  # Nginx Server Block
│           ├── deployment.yaml       # Pod Definition
│           └── service.yaml          # Internal Access
└── README.md
```
🚀 Setup & Deployment
1. Initialize the Repository
If you are starting from scratch, run these commands in your project folder:
```
git init
git add .
git commit -m "Initial commit: PHP Stack"
git branch -M main
git remote add origin [https://github.com/djkaz/argocd-php-lab.git](https://github.com/djkaz/argocd-php-lab.git)
git push -u origin main --force
```
2. Connect ArgoCD to GitHub
Apply this manifest to your cluster to tell ArgoCD to watch this repository:
```
kubectl apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: php-site-production
  namespace: argocd
spec:
  project: default
  source:
    repoURL: [https://github.com/djkaz/argocd-php-lab.git](https://github.com/djkaz/argocd-php-lab.git)
    targetRevision: HEAD
    path: charts/php-stack
  destination:
    server: [https://kubernetes.default.svc](https://kubernetes.default.svc)
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
EOF
```

3. Access the Site
Once the status in ArgoCD is Synced, run:

kubectl port-forward svc/php-service 8081:80

Open your browser at: http://localhost:8081

**🛠️ Troubleshooting**
```
Issue,Solution
Service Not Found,Ensure service.yaml is inside charts/php-stack/templates/ and pushed to GitHub.
404 Not Found,Check configmap-nginx.yaml to ensure the root path matches /var/www/html.
ArgoCD Not Syncing,"Check the ""Status"" in ArgoCD UI. Ensure the path in your Application manifest is exactly charts/php-stack.
```
