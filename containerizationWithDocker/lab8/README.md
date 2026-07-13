# Lab 8: Custom Docker Network for Microservices

## Objective

This lab demonstrates how to create a custom Docker network and enable communication between multiple containers.

## Tasks

- Clone the provided frontend and backend project.
- Create a Dockerfile for the frontend application.
- Create a Dockerfile for the backend application.
- Build Docker images for both applications.
- Create a custom Docker network named `ivolve-network` with subnet `192.168.10.0/24`.
- Run the backend container on the custom network.
- Run `frontend1` on the custom network.
- Run `frontend2` on the default Docker bridge network.
- Verify communication between the frontend and backend containers.
- Remove the custom network after stopping and removing the containers.

## Project Structure

```text
lab8/
├── Docker5/
│   ├── backend/
│   │   ├── app.py
│   │   └── Dockerfile
│   └── frontend/
│       ├── app.py
│       ├── requirements.txt
│       └── Dockerfile
├── screenshots/
└── README.md
```

## Dockerfiles

### Frontend

- Base Image: `python:3.13-alpine`
- Install dependencies from `requirements.txt`
- Expose port `5000`
- Run `app.py`

### Backend

- Base Image: `python:3.13-alpine`
- Install Flask
- Expose port `5000`
- Run `app.py`

## Commands Used

### Build Images

```bash
cd Docker5/frontend
docker build -t frontend-image .

cd ../backend
docker build -t backend-image .
```

### Create Custom Network

```bash
docker network create \
  --subnet=192.168.10.0/24 \
  ivolve-network
```

### Run Containers

```bash
docker run -d \
  --name backend \
  --network ivolve-network \
  backend-image

docker run -d \
  --name frontend1 \
  --network ivolve-network \
  -p 5000:5000 \
  frontend-image

docker run -d \
  --name frontend2 \
  -p 5001:5000 \
  frontend-image
```

### Verify Communication

```bash
curl localhost:5000
```

Output:

```text
Frontend received: Hello from Backend!
```

```bash
curl localhost:5001
```

Output:

```text
Could not connect to backend.
```

### Remove Containers and Network

```bash
docker stop backend frontend1 frontend2
docker rm backend frontend1 frontend2
docker network rm ivolve-network
```

## Result

- Successfully created a custom Docker bridge network.
- Connected the backend and `frontend1` containers to the same network.
- Verified successful communication between `frontend1` and the backend.
- Verified that `frontend2` could not communicate with the backend because it was running on the default Docker network.
- Successfully removed the custom Docker network.

## Screenshots

All screenshots for the lab are available in the `screenshots` folder.
