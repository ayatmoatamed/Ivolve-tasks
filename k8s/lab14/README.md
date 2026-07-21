# Lab 14: StatefulSet with Headless Service (MySQL)

## Overview

This lab demonstrates how to deploy a MySQL database using Kubernetes StatefulSet with a Headless Service, Secret-based configuration, tolerations, and persistent storage using Dynamic Persistent Volume Provisioning.

## Requirements

* Create a StatefulSet with one MySQL replica.
* Configure MySQL root password using Kubernetes Secret.
* Add a toleration for nodes tainted with `node=worker:NoSchedule`.
* Configure persistent storage mounted to `/var/lib/mysql`.
* Create a Headless Service for the StatefulSet.
* Verify MySQL database connectivity.

---

## Kubernetes Resources

The following resources were created:

* StatefulSet: `mysql`
* Pod: `mysql-0`
* Headless Service: `<mysql-headless-service-name>`
* Secret: `<mysql-secret-name>`
* PVC: `mysql-data-mysql-0` (created dynamically by StatefulSet)
* StorageClass: `standard`

---

# 1. StatefulSet

A StatefulSet was created to manage the MySQL database.

The StatefulSet configuration includes:

* Replica count: `1`
* Container image: `mysql:8.0`
* Persistent storage mounted at:

```
/var/lib/mysql
```

* Root password injected from Kubernetes Secret.

Example:

```yaml
          envFrom:
            - secretRef:
                name: mysql-secret1

          volumeMounts:
            - name: mysql-data
              mountPath: /var/lib/mysql
```

---

# 2. Toleration Configuration

The MySQL pod was configured to run on nodes with the following taint:

```
node=worker:NoSchedule
```

Using the following toleration:

```yaml
tolerations:
  - key: node
    operator: Equal
    value: worker
    effect: NoSchedule
```

---

# 3. Persistent Storage (Dynamic Provisioning)

Dynamic provisioning was used instead of manually creating a PersistentVolume.

The StatefulSet uses `volumeClaimTemplates` to automatically create a PVC and PV.

PVC created:

```
mysql-data-mysql-0
```

The generated PV was created automatically by the `standard` StorageClass.

Check PVC:

```bash
kubectl get pvc
```

Example output:

```
NAME                 STATUS   STORAGECLASS
mysql-data-mysql-0   Bound    standard
```

Check PV:

```bash
kubectl get pv
```

The PV is automatically generated and bound to the MySQL PVC.

---

# 4. Headless Service

A Headless Service was created to provide stable network identity for the StatefulSet pods.

Configuration:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: <mysql-headless-service-name>

spec:
  clusterIP: None

  selector:
    app: mysql
```

The service allows Kubernetes DNS discovery for the StatefulSet pod:

```
mysql-0.<service-name>
```

---

# 5. Verify Deployment

Check StatefulSet:

```bash
kubectl get statefulset
```

Expected:

```
NAME     READY
mysql    1/1
```

Check Pods:

```bash
kubectl get pods
```

Expected:

```
NAME      READY   STATUS
mysql-0   1/1     Running
```

---

# 6. Connect to MySQL Database

Enter the MySQL pod:

```bash
kubectl exec -it mysql-0 -- bash
```

Connect using MySQL client:

```bash
mysql -u root -p
```

Enter the password stored in the Kubernetes Secret.

After successful login:

```sql
SHOW DATABASES;
```

The MySQL server is operational.

---

# 7. Useful Commands

View StatefulSet details:

```bash
kubectl describe statefulset mysql
```

View Pod details:

```bash
kubectl describe pod mysql-0
```

View Services:

```bash
kubectl get svc
```

View Persistent Volumes:

```bash
kubectl get pv
```

View Persistent Volume Claims:

```bash
kubectl get pvc
```

---

## Conclusion

This lab demonstrates deploying a stateful MySQL workload on Kubernetes using:

* StatefulSet for stable pod identity.
* Headless Service for DNS-based discovery.
* Secrets for sensitive data management.
* Tolerations for node scheduling.
* Dynamic Persistent Volume provisioning for storage management.

The MySQL database was successfully deployed and verified by connecting through the MySQL client.
