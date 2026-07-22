# Lab 17: Pod Resource Management with CPU and Memory Requests and Limits

## Objective

The objective of this lab is to configure Kubernetes resource management for an existing Node.js Deployment by adding **CPU and Memory Requests and Limits**.

This lab covers:

- Configuring CPU and Memory requests and limits
- Verifying applied resources using `kubectl describe pod`
- Monitoring real-time resource usage using `kubectl top pod`
- Enabling Metrics Server to collect resource metrics

---

## Lab Requirements

Update the existing Node.js Deployment with the following resources:

### Resource Requests

Requests define the minimum amount of resources guaranteed for the container.

```yaml
requests:
  cpu: "1"
  memory: "1Gi"
```

### Resource Limits

Limits define the maximum amount of resources a container can consume.

```yaml
limits:
  cpu: "2"
  memory: "2Gi"
```

---

## 1. Update Deployment Configuration

The `resources` section was added inside the Node.js container in `deployment.yaml`.

```yaml
containers:
  - name: nodejs-container
    image: ayatmoatamed/nodejs-app:v1

    resources:
      requests:
        cpu: "1"
        memory: "1Gi"

      limits:
        cpu: "2"
        memory: "2Gi"
```

---

## 2. Apply the Updated Deployment

Apply the updated deployment:

```bash
kubectl apply -f deployment.yaml -n ivolve
```

Check the deployment status:

```bash
kubectl get deployment nodejs-app -n ivolve
```

---

## 3. Verify Pod Resources

Verify that the CPU and Memory requests and limits were successfully applied:

```bash
kubectl describe pod <pod-name> -n ivolve
```

Expected output:

```text
Limits:
  cpu:     2
  memory:  2Gi

Requests:
  cpu:     1
  memory:  1Gi
```

---

## 4. Kubernetes QoS Class

Kubernetes assigns a **Quality of Service (QoS)** class to Pods based on their resource configuration.

### Before Adding Resources

The Pod had:

```text
QoS Class: BestEffort
```

because no CPU or Memory requests or limits were defined.

### After Adding Resources

The Pod became:

```text
QoS Class: Burstable
```

because both requests and limits were configured.

Example:

```yaml
requests:
  cpu: "1"
  memory: "1Gi"

limits:
  cpu: "2"
  memory: "2Gi"
```

---

## 5. Enable Metrics Server

The `kubectl top` command requires **Metrics Server** because it collects CPU and Memory usage metrics from Kubernetes nodes and Pods.

Enable Metrics Server in Minikube:

```bash
minikube addons enable metrics-server
```

Verify that Metrics Server is running:

```bash
kubectl get pods -n kube-system | grep metrics
```

Expected output:

```text
metrics-server-xxxxx   1/1   Running
```

---

## 6. Monitor Real-Time Resource Usage

Check node resource usage:

```bash
kubectl top nodes
```

Check pod resource usage:

```bash
kubectl top pod -n ivolve
```

Example output:

```text
NAME                    CPU(cores)   MEMORY(bytes)

nodejs-app-xxxxx        1m           20Mi
nodejs-app-yyyyy        1m           20Mi
```

This shows the current CPU and Memory consumption of the running Pods.

---

## Important Concepts Learned

### CPU and Memory Requests

- Define the minimum amount of resources guaranteed for a container.
- Used by the Kubernetes Scheduler when placing Pods on nodes.

### CPU and Memory Limits

- Define the maximum amount of resources a container can consume.
- Prevent containers from using all available node resources.

---

## Deployment, ReplicaSet, and Pods Relationship

```text
Deployment
     │
ReplicaSet
     │
    Pods
```

- **Deployment** manages application updates and rollouts.
- **ReplicaSet** ensures the desired number of Pods are always running.
- **Pods** run the actual application containers.

When the Pod template changes, Kubernetes creates a new ReplicaSet and performs a **Rolling Update**.

---

## Lab Result

- ✅ Added CPU and Memory requests.
- ✅ Added CPU and Memory limits.
- ✅ Verified resources using `kubectl describe pod`.
- ✅ Enabled Metrics Server.
- ✅ Monitored resource usage using `kubectl top pod`.
- ✅ Successfully completed the Kubernetes Pod Resource Management Lab.
