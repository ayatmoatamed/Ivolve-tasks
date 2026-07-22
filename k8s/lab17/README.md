Lab 17: Pod Resource Management with CPU and Memory Requests and Limits
Objective
The objective of this lab is to configure Kubernetes resource management for an existing Node.js Deployment by adding CPU and Memory Requests and Limits.
This lab covers:
Configuring CPU and Memory requests and limits
Verifying applied resources using `kubectl describe pod`
Monitoring real-time resource usage using `kubectl top pod`
Enabling Metrics Server to collect resource metrics
---
Lab Requirements
Update the existing Node.js Deployment with the following resources:
Resource Requests
```yaml
requests:
  cpu: "1"
  memory: "1Gi"
```
Resource Limits
```yaml
limits:
  cpu: "2"
  memory: "2Gi"
```
---
1. Update Deployment Configuration
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
2. Apply the Updated Deployment
```bash
kubectl apply -f deployment.yaml -n ivolve
kubectl get deployment nodejs-app -n ivolve
```
3. Verify Pod Resources
```bash
kubectl describe pod <pod-name> -n ivolve
```
Expected:
```text
Limits:
  cpu:     2
  memory:  2Gi

Requests:
  cpu:     1
  memory:  1Gi
```
4. Kubernetes QoS Class
Before adding resources:
```text
QoS Class: BestEffort
```
After adding resources:
```text
QoS Class: Burstable
```
5. Enable Metrics Server
```bash
minikube addons enable metrics-server
kubectl get pods -n kube-system | grep metrics
```
Expected:
```text
metrics-server-xxxxx   1/1   Running
```
6. Monitor Real-Time Resource Usage
```bash
kubectl top nodes
kubectl top pod -n ivolve
```
Example:
```text
NAME                    CPU(cores)   MEMORY(bytes)
nodejs-app-xxxxx        1m           20Mi
nodejs-app-yyyyy        1m           20Mi
```
Important Concepts Learned
Requests: Minimum resources guaranteed for the container.
Limits: Maximum resources a container can consume.
Scheduler uses requests when placing Pods.
Limits prevent containers from consuming all node resources.
Deployment, ReplicaSet, and Pods Relationship
```text
Deployment
     |
 ReplicaSet
     |
    Pods
```
Deployment manages updates.
ReplicaSet maintains the desired number of Pods.
Pods run the application containers.
Updating the Pod template creates a new ReplicaSet and performs a Rolling Update.
Lab Result
✅ Added CPU and Memory requests
✅ Added CPU and Memory limits
✅ Verified resources using `kubectl describe pod`
✅ Enabled Metrics Server
✅ Monitored Pod usage using `kubectl top pod`
✅ Successfully completed Kubernetes Pod Resource Management Lab
