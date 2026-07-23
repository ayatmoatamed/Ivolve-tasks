# Lab 19: Node-Wide Pod Management with DaemonSet

## Objective

The objective of this lab is to deploy Prometheus Node Exporter as a DaemonSet so that one pod runs on every Kubernetes node and exposes node metrics on port **9100**.

---

## Prerequisites

- Minikube cluster with two nodes
- kubectl installed and configured
- Monitoring namespace

---

## Project Structure

```
lab19/
├── node-exporter-daemonset.yaml
├── README.md
└── screenshots/
    ├── create_ns.png
    ├── apply_daemonset_yamlFile.png
    ├── validate_daemonset.png
    ├── validate_pods.png
    └── metrics_exposure.png
```

---

## Step 1: Create the Monitoring Namespace

```bash
kubectl create namespace monitoring
```

Verify:

```bash
kubectl get namespaces
```

---

## Step 2: Deploy the DaemonSet

Apply the DaemonSet manifest:

```bash
kubectl apply -f node-exporter-daemonset.yaml
```

---

## Step 3: Verify the DaemonSet

```bash
kubectl get daemonset -n monitoring
```

Expected output:

- Desired Pods = Number of Nodes
- Current Pods = Number of Nodes
- Ready Pods = Number of Nodes

---

## Step 4: Validate Pods on Every Node

```bash
kubectl get pods -n monitoring -o wide
```

Expected result:

- One `node-exporter` pod running on each Kubernetes node.

Example:

| Pod | Node |
|------|------|
| node-exporter-xxxxx | minikube |
| node-exporter-yyyyy | minikube-m02 |

---

## Step 5: Confirm Metrics Exposure

Retrieve the metrics from the exporter:

```bash
curl http://localhost:9100/metrics
```

Expected output contains Prometheus metrics such as:

```text
# HELP
# TYPE
node_cpu_seconds_total
node_memory_MemAvailable_bytes
node_filesystem_size_bytes
node_network_receive_bytes_total
```

---

## Validation Commands

```bash
kubectl get daemonset -n monitoring

kubectl get pods -n monitoring -o wide

kubectl describe daemonset node-exporter -n monitoring

curl http://localhost:9100/metrics
```

---

## Expected Result

- Monitoring namespace created successfully.
- DaemonSet deployed successfully.
- One node-exporter pod scheduled on each node.
- Metrics successfully exposed on port 9100.
- The DaemonSet automatically deploys a pod on any new node added to the cluster.

---

## Screenshots

- Namespace creation
- DaemonSet deployment
- DaemonSet validation
- Pods running on every node
- Metrics exposed using `curl`

---

## Conclusion

In this lab, Prometheus Node Exporter was deployed as a DaemonSet in the `monitoring` namespace. Kubernetes automatically scheduled one exporter pod on every node in the cluster. The deployment was validated by confirming that each node was running exactly one pod and that Prometheus metrics were successfully exposed on port **9100**.
