# Lab 12: Managing Configuration and Sensitive Data with ConfigMaps and Secrets

## Objective

This lab demonstrates how to use **ConfigMaps** and **Secrets** in Kubernetes to separate application configuration from sensitive data.

### ConfigMap
Stores non-sensitive MySQL configuration:
- `DB_HOST`
- `DB_USER`

### Secret
Stores sensitive MySQL credentials:
- `DB_PASSWORD`
- `MYSQL_ROOT_PASSWORD`

The Secret values are encoded using **Base64**.

---

## Project Structure

```
lab12/
├── configmap.yaml
├── secret.yaml
├── pod1.yaml
├── screenshots/
└── README.md
```

---

## Create the ConfigMap

```bash
kubectl apply -f configmap.yaml
```

Verify:

```bash
kubectl get configmap ivolve-cm -o yaml
```

---

## Create the Secret

```bash
kubectl apply -f secret.yaml
```

Verify:

```bash
kubectl get secret ivolve-secret -o yaml
```

---

## Decode Secret Values

Decode the database password:

```bash
echo "YXlhdDEyMwo=" | base64 -d
```

Output:

```
ayat123
```

Decode the MySQL root password:

```bash
echo "aXZvbHZlMTIzCg==" | base64 -d
```

Output:

```
ivolve123
```

---

## Deploy the Pod

```bash
kubectl apply -f pod1.yaml
```

Verify:

```bash
kubectl get pods
```

---

## Verify Environment Variables

Display the environment variables inside the container:

```bash
kubectl exec -it pod1-cm -- printenv
```

Example output:

```
DB_HOST=db
DB_USER=ayat
DB_PASSWORD=ayat123
MYSQL_ROOT_PASSWORD=ivolve123
```

---

## Files Description

| File | Description |
|------|-------------|
| `configmap.yaml` | Creates the ConfigMap containing `DB_HOST` and `DB_USER`. |
| `secret.yaml` | Creates the Secret containing `DB_PASSWORD` and `MYSQL_ROOT_PASSWORD`. |
| `pod1.yaml` | Creates a Pod that loads both the ConfigMap and Secret using `envFrom`. |

---

## Key Notes

- ConfigMaps are intended for **non-sensitive configuration**.
- Secrets are intended for **sensitive data** such as passwords.
- Kubernetes stores Secret data as **Base64-encoded** values.
- When Secrets are injected into a Pod as environment variables, Kubernetes automatically decodes the values.
