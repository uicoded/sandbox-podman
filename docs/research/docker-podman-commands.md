# Docker Commands and Podman Alternatives

A comprehensive reference for Docker commands and their Podman equivalents. Most Docker commands work identically with Podman.

## Quick Setup

```bash
# Use Podman as a drop-in Docker replacement
alias docker=podman
```

---

## Container Lifecycle

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker run` | `podman run` | Create and start a container |
| `docker create` | `podman create` | Create a container without starting |
| `docker start` | `podman start` | Start a stopped container |
| `docker stop` | `podman stop` | Stop a running container |
| `docker restart` | `podman restart` | Restart a container |
| `docker rm` | `podman rm` | Remove a container |
| `docker kill` | `podman kill` | Kill a running container |
| `docker pause` | `podman pause` | Pause container processes |
| `docker unpause` | `podman unpause` | Unpause container processes |

**Common examples:**
```bash
# Run a container interactively
docker run -it ubuntu bash
podman run -it ubuntu bash

# Run in background with port mapping
docker run -d -p 8080:80 nginx
podman run -d -p 8080:80 nginx

# Run with volume mount
docker run -v /host/path:/container/path image
podman run -v /host/path:/container/path image

# Remove all stopped containers
docker rm $(docker ps -aq)
podman rm $(podman ps -aq)
```

---

## Container Inspection

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker ps` | `podman ps` | List running containers |
| `docker ps -a` | `podman ps -a` | List all containers |
| `docker logs` | `podman logs` | View container logs |
| `docker inspect` | `podman inspect` | Inspect container details |
| `docker exec` | `podman exec` | Execute command in container |
| `docker attach` | `podman attach` | Attach to running container |
| `docker top` | `podman top` | Show container processes |
| `docker stats` | `podman stats` | Show resource usage |
| `docker port` | `podman port` | Show port mappings |
| `docker diff` | `podman diff` | Show filesystem changes |
| `docker cp` | `podman cp` | Copy files to/from container |

**Common examples:**
```bash
# Follow logs
docker logs -f container_name
podman logs -f container_name

# Execute shell in running container
docker exec -it container_name bash
podman exec -it container_name bash

# Inspect container JSON
docker inspect container_name | jq '.[0].NetworkSettings'
podman inspect container_name | jq '.[0].NetworkSettings'
```

---

## Image Management

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker pull` | `podman pull` | Pull image from registry |
| `docker push` | `podman push` | Push image to registry |
| `docker build` | `podman build` | Build image from Dockerfile |
| `docker images` | `podman images` | List local images |
| `docker rmi` | `podman rmi` | Remove an image |
| `docker tag` | `podman tag` | Tag an image |
| `docker save` | `podman save` | Save image to tar archive |
| `docker load` | `podman load` | Load image from tar archive |
| `docker history` | `podman history` | Show image layers |
| `docker search` | `podman search` | Search registries |

**Common examples:**
```bash
# Build an image
docker build -t myapp:latest .
podman build -t myapp:latest .

# Pull from specific registry
docker pull docker.io/library/nginx
podman pull docker.io/library/nginx

# Save and load images
docker save myapp:latest > myapp.tar
podman save myapp:latest > myapp.tar

docker load < myapp.tar
podman load < myapp.tar

# Remove dangling images
docker image prune
podman image prune
```

---

## Volume Management

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker volume create` | `podman volume create` | Create a volume |
| `docker volume ls` | `podman volume ls` | List volumes |
| `docker volume inspect` | `podman volume inspect` | Inspect a volume |
| `docker volume rm` | `podman volume rm` | Remove a volume |
| `docker volume prune` | `podman volume prune` | Remove unused volumes |

**Common examples:**
```bash
# Create and use a named volume
docker volume create mydata
podman volume create mydata

docker run -v mydata:/data image
podman run -v mydata:/data image
```

---

## Network Management

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker network create` | `podman network create` | Create a network |
| `docker network ls` | `podman network ls` | List networks |
| `docker network inspect` | `podman network inspect` | Inspect a network |
| `docker network rm` | `podman network rm` | Remove a network |
| `docker network connect` | `podman network connect` | Connect container to network |
| `docker network disconnect` | `podman network disconnect` | Disconnect from network |

**Common examples:**
```bash
# Create a custom network
docker network create mynetwork
podman network create mynetwork

# Run container on custom network
docker run --network mynetwork image
podman run --network mynetwork image
```

---

## System Commands

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker info` | `podman info` | Display system information |
| `docker version` | `podman version` | Show version info |
| `docker system df` | `podman system df` | Show disk usage |
| `docker system prune` | `podman system prune` | Remove unused data |
| `docker events` | `podman events` | Show real-time events |
| `docker login` | `podman login` | Log in to registry |
| `docker logout` | `podman logout` | Log out from registry |

**Common examples:**
```bash
# Clean up everything unused
docker system prune -a
podman system prune -a

# Login to Docker Hub
docker login docker.io
podman login docker.io
```

---

## Docker Compose vs Podman Compose

| Docker | Podman | Description |
|--------|--------|-------------|
| `docker-compose up` | `podman-compose up` | Start services |
| `docker-compose down` | `podman-compose down` | Stop services |
| `docker-compose ps` | `podman-compose ps` | List services |
| `docker-compose logs` | `podman-compose logs` | View service logs |
| `docker-compose build` | `podman-compose build` | Build services |

**Note:** Install `podman-compose` separately: `pip install podman-compose`

---

## Podman-Only Commands

These commands are unique to Podman and have no Docker equivalent:

| Command | Description |
|---------|-------------|
| `podman pod create` | Create a pod (group of containers) |
| `podman pod start` | Start a pod |
| `podman pod stop` | Stop a pod |
| `podman pod rm` | Remove a pod |
| `podman pod ps` | List pods |
| `podman generate systemd` | Generate systemd unit files |
| `podman generate kube` | Generate Kubernetes YAML |
| `podman play kube` | Run containers from Kubernetes YAML |
| `podman machine` | Manage Podman VM (macOS/Windows) |

**Pod examples:**
```bash
# Create a pod with port mapping
podman pod create --name mypod -p 8080:80

# Add containers to pod
podman run -d --pod mypod nginx
podman run -d --pod mypod redis

# Generate systemd service
podman generate systemd --name mycontainer > mycontainer.service

# Generate Kubernetes YAML
podman generate kube mypod > mypod.yaml

# Run from Kubernetes YAML
podman play kube mypod.yaml
```

---

## Key Differences

| Aspect | Docker | Podman |
|--------|--------|--------|
| **Daemon** | Requires `dockerd` daemon | Daemonless (fork/exec) |
| **Root** | Root by default | Rootless by default |
| **Socket** | `/var/run/docker.sock` | User-specific socket |
| **Pods** | Not supported | Native pod support |
| **systemd** | Requires configuration | Built-in integration |
| **Kubernetes** | Separate tools | Native YAML support |
| **Build** | Built-in | Uses Buildah |
| **Image transfer** | Built-in | Uses Skopeo |

## Migration Tips

1. **Alias setup**: `alias docker=podman`
2. **Socket compatibility**: `export DOCKER_HOST=unix:///run/user/$(id -u)/podman/podman.sock`
3. **Rootless mode**: No configuration needed with Podman
4. **Compose**: Use `podman-compose` or `podman compose` (v4.1+)

## Sources

- [Podman Documentation](https://docs.podman.io/)
- [Docker Documentation](https://docs.docker.com/)
- [Podman Compose](https://github.com/containers/podman-compose)
