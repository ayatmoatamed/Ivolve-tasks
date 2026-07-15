# Lab 10: Node Isolation Using Taints in Kubernetes

## Overview

This lab demonstrates how to isolate a Kubernetes node using **taints**. A taint prevents pods from being scheduled on a node unless they have a matching toleration.

## Prerequisites

* Minikube installed
* kubectl installed and configured
* A running Kubernetes cluster

## Step 1: Verify the Cluster Node

```bash
kubectl get nodes
```

## Step 2: Taint the Node

Apply the taint:

```bash
kubectl taint nodes minikube node=worker:NoSchedule
```

Expected output:

```text
node/minikube tainted
```

## Step 3: Verify the Taint

Describe the node:

```bash
kubectl describe node minikube
```

Look for:

```text
Taints:
node=worker:NoSchedule
```

## Notes

* A taint prevents pods from being scheduled on the node unless they have a matching toleration.
* The `NoSchedule` effect prevents new pods without the required toleration from being scheduled on the tainted node.
