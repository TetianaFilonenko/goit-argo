# goit-argo

This repository contains Kubernetes manifests used by ArgoCD for GitOps deployment.

## Overview

This project demonstrates a GitOps workflow:

- ArgoCD is deployed in an EKS cluster
- ArgoCD monitors this repository
- Any changes pushed to Git are automatically applied to Kubernetes

## Repository Structure

```
goit-argo/
├── application.yaml
└── namespaces/
    ├── application/
    │   ├── ns.yaml
    │   └── nginx.yaml
    └── infra-tools/
        └── ns.yaml
```

## Application Deployment

ArgoCD Application is defined in `application.yaml`:

- **App name:** `nginx-app`
- **Namespace:** `infra-tools`
- **Source repo:** `TetianaFilonenko/goit-argo` (branch `main`)
- **Target path:** `namespaces/application`
- **Sync policy:** automated with prune and selfHeal enabled
- **Sync option:** `CreateNamespace=true`

To register the application with ArgoCD:

```bash
kubectl apply -f application.yaml
```

## Namespaces

- `application` — contains the nginx deployment
- `infra-tools` — ArgoCD and related tooling

## How It Works

1. You push changes to this repository
2. ArgoCD detects changes
3. ArgoCD syncs the cluster automatically
4. Kubernetes resources are created/updated

## ArgoCD Access

Run port-forward:

```
kubectl port-forward svc/argocd-server -n infra-tools 8080:443
```

Open in browser:

```
http://localhost:8080
```

## Login

Username:

```
admin
```

Password:

```
kubectl get secret argocd-initial-admin-secret -n infra-tools -o jsonpath="{.data.password}" | base64 --decode && echo
```

## Verify Deployment

Check ArgoCD application:

```
kubectl get applications -n infra-tools
```

Check pods:

```
kubectl get pods -n application
```

Expected result:

- nginx pod is running
- ArgoCD status is Synced and Healthy

## GitOps Principle

This repository acts as a single source of truth.

All Kubernetes changes must be done via Git.

Manual changes in the cluster will be reverted automatically by ArgoCD.

