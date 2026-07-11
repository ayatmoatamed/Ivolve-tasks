# Flask App Docker Environment Variables

## Overview

This project demonstrates how to build a single Flask application Docker image and run multiple containers from the same image while providing environment variables in three different ways:

1. Passing variables directly using `docker run -e`
2. Loading variables from an environment file using `--env-file`
3. Using default values defined inside the Dockerfile with `ENV`

The same Docker image is reused for all containers. Only the source of the environment variables changes.

---

## Application Source

Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/Docker-3.git

cd Docker-3
```

---

## Dockerfile

```dockerfile
FROM python:3.13-alpine

WORKDIR /app

COPY . .

RUN pip install flask

# Default environment variables
ENV APP_MODE=production
ENV APP_REGION=canada-west

EXPOSE 8080

CMD ["python", "app.py"]
```

### Design Choice

* `ENV APP_MODE=production`
* `ENV APP_REGION=canada-west`

These values are the default environment variables baked into the Docker image.

They are used when the container starts without any runtime environment variable overrides.

---

## Project Structure

```
Docker-3/
│
├── app.py
├── Dockerfile
├── staging.env
└── README.md
```

---

# Build Docker Image

Build the image once:

```bash
docker build -t flask-env-app .
```

---

# Run Containers

## Container 1: Environment Variables Using `-e`

Values:

* `APP_MODE=development`
* `APP_REGION=us-east`

Run:

```bash
docker run -d --name container1 -p 8081:8080 \
  -e APP_MODE=development \
  -e APP_REGION=us-east \
  flask-env-app
```

---

## Container 2: Environment Variables Using `.env` File

Create `staging.env`:

```env
APP_MODE=staging
APP_REGION=us-west
```

Run:

```bash
docker run -d --name container2 -p 8082:8080 \
  --env-file staging.env \
  flask-env-app
```

---

## Container 3: Using Dockerfile Default Values

No environment variables are passed during runtime.

The container uses the values defined in the Dockerfile:

* `APP_MODE=production`
* `APP_REGION=canada-west`

Run:

```bash
docker run -d --name container3 -p 8083:8080 flask-env-app
```

---

# Testing Containers

Test the Flask application:

```bash
curl http://localhost:8081
```

Expected:

```
development / us-east
```

---

```bash
curl http://localhost:8082
```

Expected:

```
staging / us-west
```

---

```bash
curl http://localhost:8083
```

Expected:

```
production / canada-west
```

---

## Verify Environment Variables Directly

Container 1:

```bash
docker exec container1 printenv | grep APP_
```

Container 2:

```bash
docker exec container2 printenv | grep APP_
```

Container 3:

```bash
docker exec container3 printenv | grep APP_
```

---

# Stop and Remove Containers

Stop all containers:

```bash
docker stop container1 container2 container3
```

Remove containers:

```bash
docker rm container1 container2 container3
```

---

# Results

| Container  | APP_MODE    | APP_REGION  | Source                    |
| ---------- | ----------- | ----------- | ------------------------- |
| container1 | development | us-east     | `-e` flags in docker run  |
| container2 | staging     | us-west     | `--env-file staging.env`  |
| container3 | production  | canada-west | Dockerfile `ENV` defaults |

