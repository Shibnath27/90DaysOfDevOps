# 📄 `docker-cheatsheet.md`

# Docker Cheat Sheet

Concise, job-ready reference.

---

## 🔹 Container Commands

```
docker run IMAGE                # Run container
docker run -it IMAGE bash       # Interactive mode
docker run -d IMAGE             # Detached mode
docker ps                       # List running containers
docker ps -a                    # List all containers
docker stop CONTAINER           # Stop container
docker rm CONTAINER             # Remove container
docker exec -it CONTAINER bash  # Execute command inside container
docker logs -f CONTAINER        # View container logs
```

---

## 🔹 Image Commands

```
docker build -t name:tag .      # Build image
docker images                   # List images
docker rmi IMAGE                # Remove image
docker pull IMAGE               # Pull from Docker Hub
docker push IMAGE               # Push to Docker Hub
docker tag SOURCE TARGET        # Tag image
```

---

## 🔹 Volume Commands

```
docker volume create NAME       # Create named volume
docker volume ls                # List volumes
docker volume inspect NAME      # Inspect volume
docker volume rm NAME           # Remove volume
```

Mount examples:

```
-v volume_name:/path            # Named volume
-v /host/path:/container/path   # Bind mount
```

---

## 🔹 Network Commands

```
docker network create NAME      # Create network
docker network ls               # List networks
docker network inspect NAME     # Inspect network
docker network connect NET CONTAINER  # Connect container
```

---

## 🔹 Docker Compose Commands

```
docker compose up -d            # Start services
docker compose down             # Stop & remove containers
docker compose down -v          # Remove containers + volumes
docker compose ps               # List services
docker compose logs -f          # View logs
docker compose build            # Build services
docker compose up --build       # Build + start
docker compose up --scale web=3 # Scale service
```

---

## 🔹 Cleanup & Disk Usage

```
docker system df                # Disk usage
docker system prune             # Remove unused data
docker system prune -a          # Remove unused images
```

---

## 🔹 Dockerfile Instructions

```
FROM        # Base image
WORKDIR     # Set working directory
COPY        # Copy files
RUN         # Execute command at build time
EXPOSE      # Document port
CMD         # Default command
ENTRYPOINT  # Fixed executable
USER        # Set runtime user
```


