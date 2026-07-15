# Kubernetes Notes

# Cloud Native Technologies

Cloud-native applications are designed to be:

- Scalable
- Highly Available
- Resilient
- Portable
- Containerized
- Microservice-based

---

# CNCF (Cloud Native Computing Foundation)

The CNCF hosts and maintains cloud-native open-source projects.

## CNCF Project Maturity Levels

### 1. Graduated

- Stable and production-ready
- Large community
- Security audits completed
- Well documented

Examples:

- Kubernetes
- Prometheus
- Helm
- Envoy

---

### 2. Incubating

- Stable enough for adoption
- Growing community
- Active development

Examples:

- OpenTelemetry
- Dragonfly

---

### 3. Sandbox

- Early-stage experimental projects
- Small community
- Under active development

---

# Popular CNCF Projects

| Project    | Purpose                     |
| ---------- | --------------------------- |
| Kubernetes | Container orchestration     |
| Helm       | Kubernetes package manager  |
| Prometheus | Monitoring & Metrics        |
| Linkerd    | Service Mesh                |
| Argo CD    | GitOps Continuous Delivery  |
| Fluentd    | Log Collection              |
| Harbor     | Container Registry          |
| etcd       | Distributed Key-Value Store |

---

# Kubernetes Architecture

Control Plane Components

- API Server
- Scheduler
- Controller Manager
- etcd

Worker Node Components

- kubelet
- kube-proxy
- Container Runtime (containerd)

---

# YAML

YAML = YAML Ain't Markup Language

Extensions

```
.yaml
.yml
```

Document separator

```yaml
---
```

Comment

```yaml
# This is a comment
```

Indentation

- Uses spaces only
- Never use tabs
- Recommended: 2 spaces

YAML Validator

https://yamlchecker.com

---

# Basic Kubernetes Objects

- Namespace
- Pod
- ReplicaSet
- Deployment
- Service
- ConfigMap
- Secret
- Ingress
- StatefulSet
- DaemonSet
- Job
- CronJob
- PersistentVolume
- PersistentVolumeClaim

---

# Namespace

Namespaces logically separate Kubernetes resources.

Default namespaces

```
default
kube-system
kube-public
kube-node-lease
```

List namespaces

```bash
kubectl get namespaces
```

or

```bash
kubectl get ns
```

Describe namespace

```bash
kubectl describe namespace default
```

---

# Namespace Manifest

```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

Create Namespace

```bash
kubectl apply -f namespace.yaml
```

Delete Namespace

```bash
kubectl delete -f namespace.yaml
```

or

```bash
kubectl delete namespace development
```

---

# Multiple Resources in One YAML

Separate resources using

```yaml
---
```

Example

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development

---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: development
```

---

# Common kubectl Commands

## Cluster Information

```bash
kubectl cluster-info
```

```bash
kubectl version
```

```bash
kubectl config view
```

Current Context

```bash
kubectl config current-context
```

All Contexts

```bash
kubectl config get-contexts
```

Switch Context

```bash
kubectl config use-context <context-name>
```

---

# Working with Pods

List Pods

```bash
kubectl get pods
```

Specific Namespace

```bash
kubectl get pods -n development
```

Wide Output

```bash
kubectl get pods -o wide
```

Watch Pods

```bash
kubectl get pods -w
```

Describe Pod

```bash
kubectl describe pod <pod-name> -n development
```

Pod Logs

```bash
kubectl logs <pod-name>
```

Follow Logs

```bash
kubectl logs -f <pod-name>
```

Execute Command

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

Delete Pod

```bash
kubectl delete pod <pod-name>
```

Delete Pod in Namespace

```bash
kubectl delete pod <pod-name> -n development
```

---

# Deployments

List Deployments

```bash
kubectl get deployments
```

Namespace

```bash
kubectl get deployments -n development
```

Describe Deployment

```bash
kubectl describe deployment <deployment-name>
```

Delete Deployment

```bash
kubectl delete deployment <deployment-name>
```

Restart Deployment

```bash
kubectl rollout restart deployment <deployment-name>
```

Deployment History

```bash
kubectl rollout history deployment <deployment-name>
```

Rollback Deployment

```bash
kubectl rollout undo deployment <deployment-name>
```

---

# Services

List Services

```bash
kubectl get svc
```

Describe Service

```bash
kubectl describe svc <service-name>
```

---

# Manifest Commands

Create Resource

```bash
kubectl apply -f file.yaml
```

Delete Resource

```bash
kubectl delete -f file.yaml
```

Preview Changes

```bash
kubectl diff -f file.yaml
```

Validate Manifest

```bash
kubectl apply --dry-run=client -f file.yaml
```

Generate YAML

```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

---

# Health Checks

Describe Pod

```bash
kubectl describe pod <pod-name>
```

View Events

```bash
kubectl get events
```

Pod Logs

```bash
kubectl logs <pod-name>
```

Resource Usage (Metrics Server Required)

```bash
kubectl top pod
```

```bash
kubectl top node
```

---

# Useful kubectl Shortcuts

```
ns = namespaces
po = pods
svc = services
deploy = deployments
rs = replicasets
cm = configmaps
pv = persistentvolumes
pvc = persistentvolumeclaims
```

Examples

```bash
kubectl get po
kubectl get svc
kubectl get deploy
kubectl get rs
```

---

# Frequently Used Flags

```
-n           Namespace
-o yaml      YAML Output
-o json      JSON Output
-w           Watch
--show-labels
--all-namespaces
```

Examples

```bash
kubectl get pods -A
```

```bash
kubectl get pods -o yaml
```

```bash
kubectl get pods --show-labels
```

---

# Kubernetes Resource Lifecycle

Write YAML

↓

kubectl apply

↓

API Server

↓

Scheduler

↓

Worker Node

↓

Container Runtime

↓

Running Pod

---

# Interview Tips

Remember these API Versions

| Resource    | API Version |
| ----------- | ----------- |
| Pod         | v1          |
| Namespace   | v1          |
| Service     | v1          |
| ConfigMap   | v1          |
| Secret      | v1          |
| Deployment  | apps/v1     |
| ReplicaSet  | apps/v1     |
| DaemonSet   | apps/v1     |
| StatefulSet | apps/v1     |
| Job         | batch/v1    |
| CronJob     | batch/v1    |

---

# Best Practices

- Prefer `kubectl apply` over `kubectl create` for declarative management.
- Store infrastructure as code in Git.
- Use namespaces to isolate environments (dev, staging, prod).
- Never store passwords directly in YAML; use Secrets.
- Use labels and selectors consistently.
- Always define resource requests and limits.
- Use liveness and readiness probes for production workloads.
- Keep manifests modular and reusable.

---

# Important Files

Kubeconfig

```
~/.kube/config
```

Minikube Config

```
minikube profile list
```

---

# Most Common Interview Commands

```bash
kubectl get all

kubectl get nodes

kubectl get pods -A

kubectl describe pod <pod>

kubectl logs <pod>

kubectl exec -it <pod> -- /bin/bash

kubectl apply -f file.yaml

kubectl delete -f file.yaml

kubectl rollout restart deployment <deployment>

kubectl rollout undo deployment <deployment>

kubectl top pod

kubectl top node

kubectl cluster-info

kubectl version

kubectl config current-context
```
