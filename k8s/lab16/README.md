# Lab 16: Kubernetes Init Container for Pre-Deployment Database Setup

## Overview

This lab demonstrates how to use a Kubernetes **Init Container** to prepare a MySQL database before starting a Node.js application.

The Init Container uses a MySQL client image to connect to an existing MySQL StatefulSet and automatically:

* Creates the `ivolve` database.
* Creates the application database user.
* Grants privileges to the user.
* Allows the main application container to start after database preparation is completed.

---

## Kubernetes Objects Used

### Namespace

```
ivolve
```

### Deployment

```
nodejs-app
```

The Node.js application runs inside a Deployment with an Init Container.

### Init Container

Image:

```
mysql:5.7
```

Purpose:

* Connect to MySQL server.
* Execute SQL commands before application startup.

Commands executed:

```sql
CREATE DATABASE IF NOT EXISTS ivolve;

CREATE USER IF NOT EXISTS 'ayat'@'%'
IDENTIFIED BY '<password>';

GRANT ALL PRIVILEGES ON ivolve.* 
TO 'ayat'@'%';

FLUSH PRIVILEGES;
```

---

## Configuration Management

### ConfigMap

Object name:

```
nodejs-configmap
```

Contains:

```yaml
DB_HOST: mysql.test.svc.cluster.local
DB_USER: ayat
```

The database host points to the MySQL StatefulSet service in the `test` namespace.

---

### Secret

Object name:

```
nodejs-secret
```

Contains:

```yaml
MYSQL_ROOT_PASSWORD
DB_PASSWORD
```

The Init Container uses the root password to connect to MySQL.

---

## Storage

The application uses a PersistentVolumeClaim:

```
nodejs-pvc
```

Mounted inside the Node.js container:

```
/app/data
```

---

## Toleration

The Deployment pod specification contains:

```yaml
tolerations:
- key: node
  operator: Equal
  value: worker
  effect: NoSchedule
```

This allows the pod to be scheduled on nodes with:

```
node=worker:NoSchedule
```

---

## Verification

Check application pods:

```bash
kubectl get pods -n ivolve
```

Expected result:

```
nodejs-app-xxxxx   1/1   Running
```

---

Connect to MySQL:

```bash
kubectl exec -it mysql-0 -n test -- bash
```

Login:

```bash
mysql -u root -p
```

Verify database:

```sql
SHOW DATABASES;
```

Verify user:

```sql
SELECT user,host FROM mysql.user;
```

Verify privileges:

```sql
SHOW GRANTS FOR 'ayat'@'%';
```

---

## Result

The Node.js application starts successfully only after the Init Container completes the database initialization process.

Lab completed successfully.
