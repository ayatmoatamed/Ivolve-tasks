# Lab 5: Multi-Stage Docker Build for Spring Boot Application

## Objective
Build and run a Spring Boot application using a **Multi-Stage Docker Build** to produce a smaller and more secure Docker image.

## Dockerfile

```dockerfile
# ---- Stage 1: Build ----
FROM maven:3.9-eclipse-temurin-17-alpine AS build

WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn package

# ---- Stage 2: Run ----
FROM amazoncorretto:17-alpine3.21

WORKDIR /app

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=build /app/target/demo-0.0.1-SNAPSHOT.jar .

USER appuser

EXPOSE 8080

CMD ["java", "-jar", "demo-0.0.1-SNAPSHOT.jar"]
```

## Build the Docker Image

```bash
docker build -t app3 .
```

## List Docker Images

```bash
docker images
```

## Run the Container

```bash
docker run -d -p 8080:8080 --name app3-container app3
```

## Test the Application

Open:

```
http://localhost:8080
```

Or use:

```bash
curl http://localhost:8080
```

## Stop the Container

