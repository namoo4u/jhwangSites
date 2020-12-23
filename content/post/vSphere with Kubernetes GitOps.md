---
title: "vSphere with Kubernetes - Tanzu Kubernetes Cluster : 선언적(Declarative) GitOPS CD"
date: 2020-05-05T00:00:00+00:00
tags: ["vsphere", "kubernetes", "k8s", "tanzu", "cd", "argocd", "argo", "gitops"]
---

# GitOps
# ArgoCD
## Installation 

```bash
kubectl create ns argocd
kubectl -n argocd apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

- Disable auth flag
```
kubectl patch deploy argocd-server -n argocd -p '[{"op": "add", "path": "/spec/template/spec/containers/0/command/-", "value": "--disable-auth"}]' --type json
```


# CD for Tanzu Kubernetes Cluster

