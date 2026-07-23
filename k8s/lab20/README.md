# Lab 20: Securing Kubernetes with RBAC and Service Accounts

## Objective

The objective of this lab is to secure Kubernetes access using **RBAC (Role-Based Access Control)** and **Service Accounts**.

In this lab, we will:

- Create a Service Account named `jenkins-sa` in the `ivolve` namespace.
- Create a token for the Service Account.
- Define a Role named `pod-reader` with read-only permissions on Pods.
- Create a RoleBinding to connect the Role with the Service Account.
- Validate that the Service Account can only list and get Pods.

---

# RBAC Overview

## What is RBAC?

RBAC (**Role-Based Access Control**) is a Kubernetes security mechanism that controls what actions users, applications, and services can perform on Kubernetes resources.

RBAC contains:

- **Role**: Defines permissions inside a specific namespace.
- **RoleBinding**: Assigns permissions from a Role to a user, group, or Service Account.
- **ServiceAccount**: Provides an identity for applications running inside Kubernetes.

---

# Lab Architecture

```
ServiceAccount
      |
      |
RoleBinding
      |
      |
Role
      |
      |
Pods
(get, list permissions)
```

The `jenkins-sa` Service Account will have permission to read Pods only inside the `ivolve` namespace.

---

# Files Structure

```
lab20/
│
├── service-account.yaml
├── role.yaml
├── role-binding.yaml
└── screenshots/
```

---

# 1. Create Service Account

## service-account.yaml

```yaml
apiVersion: v1
kind: ServiceAccount

metadata:
  name: jenkins-sa
  namespace: ivolve
```

## Explanation

This creates a Service Account named:

```
jenkins-sa
```

inside:

```
ivolve namespace
```

The Service Account can be used by Jenkins or any application that needs access to the Kubernetes API.

---

# 2. Create Role

## role.yaml

```yaml
apiVersion: rbac.authorization.k8s.io/v1

kind: Role

metadata:
  name: pod-reader
  namespace: ivolve

rules:
- apiGroups: [""]
  resources:
  - pods
  verbs:
  - get
  - list
```

## Explanation

The Role defines what actions are allowed.

This Role gives read-only access to Pods:

| Resource | Permission |
|----------|------------|
| Pods | get |
| Pods | list |

The Service Account cannot:

- Create Pods
- Delete Pods
- Update Pods

---

# 3. Create RoleBinding

## role-binding.yaml

```yaml
apiVersion: rbac.authorization.k8s.io/v1

kind: RoleBinding

metadata:
  name: read-pods-binding
  namespace: ivolve

subjects:

- kind: ServiceAccount
  name: jenkins-sa

roleRef:

  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Explanation

RoleBinding connects:

```
jenkins-sa ServiceAccount
```

with:

```
pod-reader Role
```

After creating the RoleBinding, the Service Account receives the permissions defined in the Role.

---

# Deployment Steps

## 1. Create Namespace

```bash
kubectl create namespace ivolve
```

---

## 2. Create Service Account

```bash
kubectl apply -f service-account.yaml
```

Check:

```bash
kubectl get serviceaccount -n ivolve
```

Expected:

```
NAME
jenkins-sa
```

---

## 3. Create Role

```bash
kubectl apply -f role.yaml
```

Check:

```bash
kubectl get role -n ivolve
```

Expected:

```
NAME
pod-reader
```

---

## 4. Create RoleBinding

```bash
kubectl apply -f role-binding.yaml
```

Check:

```bash
kubectl get rolebinding -n ivolve
```

Expected:

```
NAME
read-pods-binding
```

---

# Create Service Account Token

Create a token for the Service Account:

```bash
kubectl create token jenkins-sa -n ivolve
```

Example output:

```
eyJhbGciOiJSUzI1NiIs...
```

This token can be used to authenticate as `jenkins-sa`.

---

# Validate RBAC Permissions

## Check if Service Account can list Pods

```bash
kubectl auth can-i list pods \
--as=system:serviceaccount:ivolve:jenkins-sa \
-n ivolve
```

Expected result:

```
yes
```

---

## Check if Service Account can get Pods

```bash
kubectl auth can-i get pods \
--as=system:serviceaccount:ivolve:jenkins-sa \
-n ivolve
```

Expected result:

```
yes
```

---

## Check Create Permission

```bash
kubectl auth can-i create pods \
--as=system:serviceaccount:ivolve:jenkins-sa \
-n ivolve
```

Expected result:

```
no
```

---

## Check Delete Permission

```bash
kubectl auth can-i delete pods \
--as=system:serviceaccount:ivolve:jenkins-sa \
-n ivolve
```

Expected result:

```
no
```

---

# Validation Result

The Service Account:

```
jenkins-sa
```

has only read permissions.

Allowed:

✅ get Pods  
✅ list Pods  


Not Allowed:

❌ create Pods  
❌ delete Pods  
❌ update Pods  

---

# Key Concepts Learned

## Service Account

A Kubernetes identity used by applications and workloads.

Example:

```
Jenkins
   |
   |
jenkins-sa
   |
   |
Kubernetes API
```

---

## Role

Defines permissions.

Example:

```
pod-reader

pods:
 - get
 - list
```

---

## RoleBinding

Connects the identity with permissions.

Example:

```
jenkins-sa
     |
     |
pod-reader Role
```

---

# Conclusion

In this lab, RBAC was configured to provide secure and limited access to Kubernetes resources.

A dedicated Service Account named `jenkins-sa` was created and assigned a Role that allows only reading Pods inside the `ivolve` namespace.

This follows the principle of **least privilege**, where users and applications receive only the permissions they need.
