# Lab 12: Managing Configuration and Sensitive Data with ConfigMaps and Secrets

## Overview

This lab demonstrates how to manage application configuration and sensitive data in Kubernetes using **ConfigMaps** and **Secrets**.

- **ConfigMap** is used to store non-sensitive MySQL configuration variables.
- **Secret** is used to store sensitive MySQL credentials securely using Base64 encoding.
- The Pod consumes values from both ConfigMap and Secret as environment variables.

---

## Objectives

In this lab, we will:

- Create a ConfigMap to store MySQL configuration variables:
  - `DB_HOST` – The hostname of the MySQL StatefulSet service.
  - `DB_USER` – The database user that the application uses to connect to the ivolve database.

- Create a Secret to store sensitive MySQL credentials:
  - `DB_PASSWORD` – The password for the database user.
  - `MYSQL_ROOT_PASSWORD` – The root password for the MySQL database.

- Use Base64 encoding for Secret data values.
- Inject ConfigMap and Secret values into a Kubernetes Pod.

---

## Lab Files Structure

```
lab12/
│
├── configmap.yaml
├── secret.yaml
├── pod4.yaml
└── README.md
```

---

# 1. Creating the ConfigMap

The ConfigMap stores non-sensitive MySQL configuration values.

### Apply ConfigMap

```bash
kubectl apply -f configmap.yaml
```

### Verify ConfigMap

```bash
kubectl get configmap ivolve-cm -o yaml
```

Example:

```yaml
data:
  DB_HOST: db
  DB_USER: ayat
```

---

# 2. Creating the Secret

The Secret stores sensitive MySQL credentials.

Secret values are stored using Base64 encoding.

## Encode Secret Values

Encode the MySQL root password:

```bash
echo -n "ivolve123" | base64
```

Output:

```
aXZvbHZlMTIz
```

Encode the database password:

```bash
echo -n "ayat123" | base64
```

Output:

```
YXlhdDEyMw==
```

---

## Apply Secret

```bash
kubectl apply -f secret.yaml
```

---

## Verify Secret

```bash
kubectl get secret ivolve-secret -o yaml
```

Example:

```yaml
data:
  DB_PASSWORD: YXlhdDEyMw==
  MYSQL_ROOT_PASSWORD: aXZvbHZlMTIz
```

---

# 3. Decode Secret Values

Base64 encoded values can be decoded using:

```bash
echo "aXZvbHZlMTIz" | base64 -d
```

Output:

```
ivolve123
```

Example:

```bash
echo "YXlhdDEyMw==" | base64 -d
```

Output:

```
ayat123
```

---

# 4. Using ConfigMap and Secret in Pod

The Pod loads configuration values from ConfigMap and Secret using environment variables.

ConfigMap:

```yaml
envFrom:
- configMapRef:
    name: ivolve-cm
```

Secret:

```yaml
envFrom:
- secretRef:
    name: ivolve-secret
```

---

# 5. Deploy the Pod

Apply the Pod configuration:

```bash
kubectl apply -f pod4.yaml
```

Check Pod status:

```bash
kubectl get pods
```

Expected output:

```
NAME       READY   STATUS
pod4-cm    1/1     Running
```

---

# 6. Verify Environment Variables Inside the Container

Execute into the container:

```bash
kubectl exec -it pod4-cm -- printenv
```

Expected output:

```
DB_HOST=db
DB_USER=ayat
DB_PASSWORD=ayat123
MYSQL_ROOT_PASSWORD=ivolve123
```

---

# Important Notes

- ConfigMaps are used to store **non-sensitive configuration data**.
- Secrets are used to store **sensitive information** such as passwords and tokens.
- Kubernetes Secrets use Base64 encoding by default, which is **encoding and not encryption**.
- Kubernetes automatically decodes Base64 values when injecting them into container environment variables.

---

# Useful Commands Used

```bash
kubectl apply -f configmap.yaml

kubectl apply -f secret.yaml

kubectl get configmap ivolve-cm -o yaml

kubectl get secret ivolve-secret -o yaml

kubectl exec -it pod4-cm -- printenv

echo "value" | base64 -d
```

---

# Conclusion

In this lab, Kubernetes ConfigMaps and Secrets were successfully used to manage application configuration and sensitive data.

- ConfigMap managed MySQL configuration variables.
- Secret stored MySQL credentials securely.
- Base64 encoding was used for Secret data.
- The Pod successfully consumed values from both ConfigMap and Secret.
