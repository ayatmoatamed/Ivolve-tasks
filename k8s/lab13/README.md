# Lab 13: Persistent Storage Setup for Application Logging

## Overview

This lab demonstrates how to configure persistent storage in Kubernetes using:

* Persistent Volume (PV)
* Persistent Volume Claim (PVC)

The goal is to provide persistent storage for application logs so that data is not lost when Pods are restarted or recreated.

---

## Architecture

```
Pod
 |
 | uses
 v
PVC (Persistent Volume Claim)
 |
 | binds to
 v
PV (Persistent Volume)
 |
 | uses
 v
Node filesystem
/mnt/app-logs
```

---

# 1. Persistent Volume (PV)

A Persistent Volume represents the actual storage resource available in the Kubernetes cluster.

In this lab, the PV uses a `hostPath` storage type, which means it uses a directory from the Kubernetes node filesystem.

### PV Specifications

| Configuration  | Value         |
| -------------- | ------------- |
| Name           | app-logs-pv   |
| Storage Size   | 1Gi           |
| Storage Type   | hostPath      |
| Path           | /mnt/app-logs |
| Access Mode    | ReadWriteMany |
| Reclaim Policy | Retain        |

---

## PV Configuration

File: `pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume

metadata:
  name: app-logs-pv

spec:
  capacity:
    storage: 1Gi

  accessModes:
    - ReadWriteMany

  persistentVolumeReclaimPolicy: Retain

  hostPath:
    path: /mnt/app-logs
```

Apply the PV:

```bash
kubectl apply -f pv.yaml
```

Check PV status:

```bash
kubectl get pv
```

---

# 2. Persistent Volume Claim (PVC)

A Persistent Volume Claim is a request for storage made by applications.

The PVC requests storage from an available PV that matches its requirements.

### PVC Specifications

| Configuration     | Value         |
| ----------------- | ------------- |
| Name              | app-logs-pvc  |
| Requested Storage | 1Gi           |
| Access Mode       | ReadWriteMany |

---

## PVC Configuration

File: `pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: app-logs-pvc

spec:
  accessModes:
    - ReadWriteMany

  resources:
    requests:
      storage: 1Gi
```

Apply the PVC:

```bash
kubectl apply -f pvc.yaml
```

Check PVC status:

```bash
kubectl get pvc
```

---

# 3. HostPath Directory

The PV uses the directory:

```
/mnt/app-logs
```

This directory must exist on the Kubernetes node.

For Minikube:

Enter the node:

```bash
minikube ssh
```

Create the directory:

```bash
sudo mkdir -p /mnt/app-logs
```

Exit:

```bash
exit
```

---

# 4. Verify PV and PVC Binding

Check both resources:

```bash
kubectl get pv,pvc
```

Expected result:

```
PV:
app-logs-pv     Bound

PVC:
app-logs-pvc    Bound
```

When the PVC status becomes `Bound`, the PVC is successfully connected to the PV.

---

# Key Concepts

## Persistent Volume (PV)

* Kubernetes object representing available storage.
* Defines storage size, access mode, and storage location.
* Uses the real storage resource.

## Persistent Volume Claim (PVC)

* A request for storage from an application.
* Connects Pods to Persistent Volumes.

## hostPath

* Storage type that uses a directory from the node filesystem.
* In this lab:

```
hostPath → /mnt/app-logs
```

## ReadWriteMany (RWX)

Allows multiple Pods to read and write to the same volume.

## Retain Policy

Keeps the stored data even after the PVC is deleted.

---

## Lab Result

Successfully created:

✅ Persistent Volume
✅ Persistent Volume Claim
✅ RWX storage configuration
✅ Persistent storage location for application logs
