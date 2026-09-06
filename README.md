# GitOps Manifests Repository (`gitops-manifests`)

This repository contains the **Helm Chart** (`helm/hello-app`) and **ArgoCD Application configuration** (`argocd/application.yaml`) for automated GitOps deployment.

---

## Step 1: Install ArgoCD in Your Kubernetes (EKS) Cluster

- **Intention**: Install the ArgoCD continuous deployment tool into your cluster so it can watch your repository for updates.
- **Command**:
  ```bash
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  ```
- **Verification command**:
  ```bash
  kubectl get pods -n argocd
  ```
  *Expected Output*: All ArgoCD pods (e.g. `argocd-server`, `argocd-repo-server`) should show status `Running`.

---

## Step 2: Get the ArgoCD Admin Password

- **Intention**: Retrieve the secret password created by ArgoCD so you can log into the web dashboard or CLI.
- **Command**:
  - *On Windows (PowerShell)*:
    ```powershell
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | % { [System.Text.Encoding]::UTF-8.GetString([System.Convert]::FromBase64String($_)) }
    ```
  - *On Linux / macOS*:
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```
- **Verification command**:
  ```bash
  echo "Password retrieved successfully!"
  ```
  *Expected Output*: Printed text string containing your admin password.

---

## Step 3: Deploy the Application using ArgoCD

- **Intention**: Tell ArgoCD to read the Helm chart from this Git repository and deploy the frontend and backend microservices automatically.
- **Command**:
  ```bash
  kubectl apply -f argocd/application.yaml
  ```
- **Verification command**:
  ```bash
  kubectl get application hello-world-app -n argocd
  ```
  *Expected Output*: Status should show `Synced` and `Healthy`.

---

## Step 4: Verify Running Application Pods and Services

- **Intention**: Check that the Angular frontend and FastAPI backend containers are successfully running inside the cluster.
- **Command**:
  ```bash
  kubectl get pods,svc,ingress -n hello-app
  ```
- **Verification command**:
  ```bash
  kubectl get pods -n hello-app -l app=frontend
  kubectl get pods -n hello-app -l app=backend
  ```
  *Expected Output*: Both frontend and backend pods should show status `Running` with `2/2` replicas ready.
