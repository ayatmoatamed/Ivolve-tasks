# Lab 17: Pod Resource Management with CPU and Memory Requests and Limits

## Objective

The objective of this lab is to configure Kubernetes resource management for an existing Node.js Deployment by adding CPU and Memory requests and limits.

The lab also covers:
- Verifying applied resources using `kubectl describe pod`
- Monitoring real-time resource usage using `kubectl top pod`
- Enabling Metrics Server to collect resource metrics

---

# Lab Requirements

Update the existing Node.js Deployment with the following resources:

## Resource Requests

Requests define the minimum amount of resources guaranteed for the container.

```yaml
cpu: "1"
memory: "1Gi"
Resource Limits

Limits define the maximum amount of resources the container can consume.

cpu: "2"
memory: "2Gi"
1. Update Deployment Configuration

The resources section was added inside the Node.js container in deployment.yaml:

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
2. Apply the Updated Deployment

Apply the updated Deployment:

kubectl apply -f deployment.yaml -n ivolve

Check the Deployment status:

kubectl get deployment nodejs-app -n ivolve
3. Verify Pod Resources

To verify that CPU and Memory requests and limits were applied:

kubectl describe pod <pod-name> -n ivolve

Expected output:

Limits:
  cpu:     2
  memory:  2Gi

Requests:
  cpu:     1
  memory:  1Gi
4. Kubernetes QoS Class

Kubernetes assigns a Quality of Service (QoS) class to Pods depending on resource configuration.

Before adding resources

The Pod had:

QoS Class: BestEffort

because no CPU or Memory requests/limits were defined.

After adding resources

The Pod became:

QoS Class: Burstable

because requests and limits were configured.

Example:

requests:
  cpu: "1"
  memory: "1Gi"

limits:
  cpu: "2"
  memory: "2Gi"
5. Enable Metrics Server

The kubectl top command requires Metrics Server because it collects CPU and Memory usage metrics from Kubernetes nodes and Pods.

Enable Metrics Server in Minikube:

minikube addons enable metrics-server

Verify that Metrics Server is running:

kubectl get pods -n kube-system | grep metrics

Expected output:

metrics-server-xxxxx   1/1   Running
6. Monitor Real-Time Resource Usage

After Metrics Server is running, resource usage can be monitored using:

Check Node Resources
kubectl top nodes
Check Pod Resources
kubectl top pod -n ivolve

Example output:

NAME                          CPU(cores)   MEMORY(bytes)

nodejs-app-xxxxx              1m           20Mi
nodejs-app-yyyyy              1m           20Mi

This shows the current CPU and Memory consumption of running Pods.

Useful Kubernetes Commands
Get Pods
kubectl get pods -n ivolve
Describe Pod
kubectl describe pod <pod-name> -n ivolve
Check Deployment
kubectl get deployment nodejs-app -n ivolve
Check ReplicaSets
kubectl get rs -n ivolve
Check Rollout Status
kubectl rollout status deployment nodejs-app -n ivolve
Monitor Resource Usage
kubectl top pod -n ivolve
Important Concepts Learned
CPU and Memory Requests
Requests are the minimum resources guaranteed for the container.
Kubernetes Scheduler uses requests when deciding where to place Pods.
CPU and Memory Limits
Limits define the maximum resources a container can use.
They prevent a container from consuming all available node resources.
Deployment, ReplicaSet, and Pods Relationship

The hierarchy in Kubernetes:

Deployment
     |
     |
 ReplicaSet
     |
     |
 Pods
Deployment manages application updates.
ReplicaSet maintains the desired number of Pods.
Pods run the actual containers.

When the Pod template changes, Kubernetes creates a new ReplicaSet and performs a rolling update.

Lab Result

✅ Added CPU and Memory requests
✅ Added CPU and Memory limits
✅ Verified resources using kubectl describe pod
✅ Enabled Metrics Server
✅ Monitored Pod usage using kubectl top pod
✅ Successfully completed Kubernetes Pod Resource Management Lab
