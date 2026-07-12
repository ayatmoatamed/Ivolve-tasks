# Lab 7: Docker Volume and Bind Mount with Nginx

## Objective
This lab demonstrates how to use **Docker Volumes** and **Bind Mounts** with an Nginx container.

## Tasks
- Create a Docker volume named `nginx_logs`.
- Verify the volume location using `docker volume inspect`.
- Create a custom `index.html` file.
- Run an Nginx container with:
  - A Docker Volume mounted to `/var/log/nginx`
  - A Bind Mount mounted to `/usr/share/nginx/html`
- Verify the web page using `curl`.
- Modify the local `index.html` file and verify the changes without recreating the container.
- Verify that the volume is attached to the container.
- Remove the container and delete the Docker volume.

## Project Structure

```text
lab7/
├── index.html
├── README.md
└── screenshots/
```

## Commands Used

### Create Docker Volume

```bash
docker volume create nginx_logs
docker volume inspect nginx_logs
```

### Run Nginx Container

```bash
docker run -d \
  --name container \
  -p 8080:80 \
  -v nginx_logs:/var/log/nginx \
  -v "$(pwd)/index.html:/usr/share/nginx/html/index.html" \
  nginx
```

### Verify Nginx Page

```bash
curl localhost:8080
```

### Verify Volume

```bash
docker inspect container
docker volume inspect nginx_logs
```

### Remove Container and Volume

```bash
docker stop container
docker rm container
docker volume rm nginx_logs
```

## Result

- Successfully created and mounted a Docker Volume.
- Successfully mounted a local HTML file using a Bind Mount.
- Verified that changes made to the local `index.html` file were reflected immediately in the running Nginx container.
- Successfully removed the container and deleted the Docker volume.

## Screenshots

Screenshots are available in the `screenshots` folder.
