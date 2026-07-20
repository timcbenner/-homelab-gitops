# Raspberry Pi K3s Homelab

## Overview

This repository contains the GitOps configuration for my Raspberry Pi Kubernetes homelab.

The cluster is built using **K3s**, managed with **ArgoCD**, monitored with **Prometheus/Grafana**, and uses **Longhorn** for distributed persistent storage.

Primary goals:

- Learn Kubernetes and GitOps
- Build a production-style infrastructure
- Experiment with security tools
- Host self-managed services
- Build an AI-ready platform

---

# Cluster Architecture

| Hostname | Hardware | Role | Labels |
|-----------|----------|------|--------|
| **pi-master** | Raspberry Pi 5 (4 GB) | K3s Control Plane | `workload=general` |
| **pi-monitor** | Raspberry Pi 4 (8 GB) | Worker / Monitoring | `workload=monitoring` |
| **pi-worker** | Raspberry Pi 3 (1 GB) | Worker | `workload=general` |

---

# Kubernetes

- Distribution: **K3s**
- Container Runtime: **containerd**
- Ingress Controller: **Traefik**

---

# GitOps

## ArgoCD

Namespace:

```text
gitops
```

Repository:

```text
https://github.com/timcbenner/-homelab-gitops.git
```

Access:

```text
https://<pi-master-ip>:30443
```

Default username:

```text
admin
```

Retrieve password:

```bash
sudo kubectl -n gitops get secret argocd-initial-admin-secret \
-o jsonpath='{.data.password}' | base64 -d
echo
```

---

# Storage

## Longhorn

Namespace:

```text
longhorn-system
```

Provides:

- Distributed block storage
- Persistent Volumes
- CSI StorageClass
- Volume snapshots
- Replication

Check status:

```bash
kubectl get pods -n longhorn-system
```

Storage Classes:

```bash
kubectl get storageclass
```

---

# Monitoring

Namespace:

```text
monitoring
```

Installed:

- Prometheus
- Alertmanager
- Grafana
- kube-state-metrics
- node-exporter

Useful commands:

```bash
kubectl get pods -n monitoring
```

---

# Homepage

Namespace:

```text
homepage
```

Ingress:

```text
homepage.home
```

---

# Namespaces

```text
management
monitoring
security
gitops
registry
ai
networking
homepage
longhorn-system
```

---

# Useful Commands

## Cluster

```bash
kubectl get nodes
kubectl get pods -A
kubectl top nodes
kubectl top pods -A
```

---

## ArgoCD

```bash
kubectl get applications -n gitops

kubectl get application longhorn -n gitops

kubectl annotate application longhorn \
-n gitops \
argocd.argoproj.io/refresh=hard \
--overwrite
```

---

## Longhorn

```bash
kubectl get pods -n longhorn-system

kubectl get volumes.longhorn.io -n longhorn-system

kubectl get nodes.longhorn.io
```

---

## Monitoring

```bash
kubectl get pods -n monitoring

kubectl logs -n monitoring deployment/prometheus

kubectl logs -n monitoring deployment/grafana
```

---

## Troubleshooting

### Node Status

```bash
kubectl get nodes

kubectl describe node <node>
```

### Pod Status

```bash
kubectl describe pod <pod> -n <namespace>

kubectl logs <pod> -n <namespace>
```

### Events

```bash
kubectl get events \
--sort-by=.metadata.creationTimestamp
```

### Restart a Deployment

```bash
kubectl rollout restart deployment <deployment> -n <namespace>
```

---

# Backup Strategy

GitOps configuration is stored in GitHub.

Regularly back up:

- Longhorn volumes
- Kubernetes manifests
- Git repository
- Raspberry Pi OS images

---

# Future Roadmap

## Infrastructure

- [ ] cert-manager
- [ ] ExternalDNS
- [ ] Authentik
- [ ] MinIO
- [ ] Harbor Registry
- [ ] OpenBao (Vault)

## Security

- [ ] Wazuh
- [ ] Falco
- [ ] DefectDojo
- [ ] Trivy
- [ ] Kyverno
- [ ] Open Policy Agent

## Observability

- [ ] Loki
- [ ] Promtail
- [ ] Tempo
- [ ] OpenTelemetry

## AI

- [ ] Open WebUI
- [ ] Ollama
- [ ] LiteLLM
- [ ] Continue.dev integration

## Automation

- [ ] Renovate
- [ ] ArgoCD Image Updater
- [ ] GitHub Actions
- [ ] Automated backups

---

# Repository Structure

```text
.
├── apps/
├── argocd-apps/
├── manifests/
├── README.md
└── docs/
```

---

# Lessons Learned

- Use GitOps for all cluster changes.
- Validate YAML before committing.
- Keep infrastructure changes in Git.
- Label nodes intentionally to control scheduling.
- Test changes in a small scope before rolling them out cluster-wide.
- Create a Git commit after each stable milestone.

---

# Version History

## v1.0

- K3s cluster operational
- Three Raspberry Pi nodes joined
- Traefik configured
- ArgoCD installed
- Longhorn installed
- Prometheus and Grafana deployed
- Homepage deployed
- GitOps repository established
