---
title: "Docker Command Cheatsheet"
date: '2025-10-17T17:11:08+02:00'
draft: false
tags: ["docker","cheatsheet","containers"]
---

A compact, practical Docker command reference for everyday container work — copy commands and adapt them to your setup.

<!--more-->

## Quick setup

Install Docker Desktop (Windows/macOS) or Docker Engine (Linux). Verify:

```bash
docker --version
docker info
```

## Images

Search, pull, list, remove:

```bash
docker search nginx
docker pull nginx:latest
docker images           # list local images
docker rmi image:tag    # remove image
```

Build an image from a Dockerfile:

```bash
docker build -t myapp:1.0 .
docker build --no-cache -t myapp:latest .
```

Tag & push:

```bash
docker tag myapp:1.0 myrepo/myapp:1.0
docker push myrepo/myapp:1.0
```

## Containers

Run, list, inspect, stop, remove:

```bash
docker run --name web -d -p 8080:80 nginx:latest
docker ps               # running containers
docker ps -a            # all containers
docker logs -f web
docker exec -it web /bin/sh
docker stop web
docker rm web
```

Run with volumes & env:

```bash
docker run -d --name db -e POSTGRES_PASSWORD=secret -v db_data:/var/lib/postgresql/data postgres:15
```

Detach mode vs foreground:

- Foreground: omit -d
- Attach later: docker attach <container>

## Dockerfile essentials

Minimal example:

```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY . .
CMD ["node","server.js"]
EXPOSE 3000
```

Best practices:

- Use small base images (alpine/distroless)
- Leverage multi-stage builds for artifacts
- Pin major versions for reproducible builds
- Minimize layers and files copied

## Docker Compose (v2+)

Compose file (docker-compose.yml):

```yaml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8080:3000"
    volumes:
      - ./:/app
    environment:
      - NODE_ENV=development
  db:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=secret
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

Useful commands:

```bash
docker compose up --build
docker compose up -d
docker compose ps
docker compose logs -f
docker compose down --volumes
```

## Cleanup & disk space

List dangling images and prune:

```bash
docker images -f dangling=true
docker system prune           # careful: removes stopped containers, networks, dangling images
docker system prune -a        # also removes unused images
docker volume ls
docker volume rm <name>
```

Check disk usage:

```bash
docker system df
```

## Debugging & networking

Inspect container details:

```bash
docker inspect <container>
docker network ls
docker network inspect bridge
docker exec -it <container> /bin/sh
```

Port forwarding: map host:container with -p HOST:CONTAINER.

## Security & permissions

- Avoid running processes as root inside containers.
- Minimize secret exposure; use environment variables or secret managers.
- Scan images with tools (Docker scan / trivy).

## Quick aliases & tips

- Start a shell in a one-off container:
  docker run --rm -it -v "$(pwd)":/work -w /work node:18 bash
- Rebuild & restart: docker compose up --build -d
- View combined logs: docker compose logs -f

## Helpful resources

- Official docs: <https://docs.docker.com/>
- Dockerfile best practices: <https://docs.docker.com/develop/develop-images/dockerfile_best-practices/>

That's it — keep this post handy for quick Docker commands.
