# Lab 11: Namespace Management and Resource Quota Enforcement

## Objective

This lab demonstrates how to manage Kubernetes namespaces and enforce resource limits using a **ResourceQuota**.

## Prerequisites

* Minikube installed and running
* kubectl installed and configured
* A running Kubernetes cluster

## Tasks

### 1. Create a Namespace

Create a namespace named **ivolve**.

```bash
kubectl create namespace ivolve
```

Verify the namespace:

```bash
kubectl get namespaces
```

---

### 2. Create a ResourceQuota

Create a file named `resourcequota.yaml` with the following content:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: pod-limit
  namespace: ivolve
spec:
  hard:
    pods: "2"
```

Apply the ResourceQuota:

```bash
kubectl apply -f resourcequota.yaml
```

---

### 3. Verify the ResourceQuota

List the ResourceQuota:

```bash
kubectl get resourcequota -n ivolve
```

Display its details:

```bash
kubectl describe resourcequota pod-limit -n ivolve
```

## Result

* Successfully created the `ivolve` namespace.
* Applied a ResourceQuota limiting the namespace to a maximum of **2 Pods**.
* Verified the quota configuration using Kubernetes commands.

## Screenshots

Screenshots demonstrating each step are available in the `screenshots` directory.
