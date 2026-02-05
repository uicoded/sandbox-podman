---
title: Podman - The Docker Alternative
---

# Podman
## The Docker Alternative

Daemonless | Rootless | Open Source

---

## Agenda

1. Introduction to Containerization
2. Meet Podman
3. Architecture Deep Dive
4. Security Comparison
5. Command Compatibility
6. Podman Ecosystem
7. Podman-Exclusive Features
8. Practical Guidance
9. Migration Tips

---

## What is Containerization?

Packaging applications with their dependencies into isolated, portable units.

| Concept | Description |
|---------|-------------|
| **Image** | Read-only template with app + dependencies |
| **Container** | Running instance of an image |
| **Registry** | Repository for storing/sharing images |
| **Runtime** | Software that runs containers |

---

## Docker's Dominance

Docker revolutionized containerization (2013)

- Simplified container creation and management
- Introduced Dockerfile for reproducible builds
- Created Docker Hub ecosystem
- Became the de facto standard

**But...** the daemon-based architecture has limitations.

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Meet Podman

---

## What is Podman?

**Pod Manager** - An open-source container engine by Red Hat

- First released in 2018
- Part of the `containers` project (GitHub)
- Fully OCI-compliant
- Drop-in Docker replacement

```bash
# It's this simple
alias docker=podman
```

---

## Podman Philosophy

> "Run anywhere, run secure"

| Principle | Implementation |
|-----------|----------------|
| **No daemon** | Fork/exec model |
| **No root** | Rootless by default |
| **Standards** | OCI-compliant images & runtime |
| **Modularity** | Separate tools for specific tasks |

---

## Key Features

- **Daemonless** - No background service required
- **Rootless** - Run containers without root privileges
- **Pod support** - Native Kubernetes-style pods
- **systemd integration** - Generate unit files automatically
- **Docker compatible** - Same CLI, same images

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Architecture Deep Dive

---

## Docker Architecture

![Docker Architecture](assets/docker-architecture.jpg)

**Daemon-based**: All commands go through `dockerd`

---

## Docker Components

| Component | Role |
|-----------|------|
| **Docker CLI** | User interface, sends commands to daemon |
| **dockerd** | Central daemon managing all operations |
| **containerd** | Container lifecycle management |
| **runc** | OCI runtime, spawns containers |

**Key characteristic**: Single point of failure (daemon)

---

## Podman Architecture

![Podman Architecture](assets/podman-architecture.jpg)

**Daemonless**: Direct fork/exec to runtime

---

## Podman Components

| Component | Role |
|-----------|------|
| **Podman CLI** | User interface, forks directly |
| **conmon** | Container monitor, handles I/O |
| **OCI Runtime** | runc or crun (faster, C-based) |
| **Buildah** | Image building (optional) |
| **Skopeo** | Image transfer (optional) |

**Key characteristic**: Each container is an independent process

---

## Architecture Comparison

| Aspect | Docker | Podman |
|--------|--------|--------|
| **Model** | Client-Server | Fork/Exec |
| **Daemon** | Required (dockerd) | None |
| **Single point of failure** | Yes (daemon) | No |
| **Container parent** | containerd-shim | conmon |
| **Socket** | `/var/run/docker.sock` | User socket |

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Security

---

## Docker Security Model

| Aspect | Docker Default |
|--------|----------------|
| **Daemon** | Runs as root |
| **Containers** | Root inside by default |
| **Capabilities** | 14 Linux capabilities |
| **Socket** | Root-owned, group access |

**Risk**: Daemon compromise = full host access

```bash
# Docker socket gives root-equivalent access
docker run -v /:/host alpine cat /host/etc/shadow
```

---

## Podman Security Model

| Aspect | Podman Default |
|--------|----------------|
| **Daemon** | None |
| **Containers** | User namespaces |
| **Capabilities** | 11 Linux capabilities |
| **Socket** | Per-user, no root |

**Benefit**: Container root maps to unprivileged user

```bash
# Rootless: container root = your user
podman run alpine whoami  # root (but mapped to UID 1000)
```

---

## Security Comparison

| Feature | Docker | Podman |
|---------|--------|--------|
| Root daemon | Yes | No |
| Rootless mode | Optional | Default |
| User namespaces | Optional | Default |
| Capabilities | 14 | 11 |
| Socket exposure | System-wide | Per-user |
| Attack surface | Larger | Smaller |

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Command Compatibility

---

## Drop-in Replacement

Podman uses the same CLI syntax as Docker.

```bash
# Option 1: Alias
alias docker=podman

# Option 2: Socket compatibility
export DOCKER_HOST=unix:///run/user/$(id -u)/podman/podman.sock

# Option 3: Symlink (system-wide)
ln -s /usr/bin/podman /usr/local/bin/docker
```

Most Docker commands work identically.

---

## Why Are They Compatible?

Both Docker and Podman implement **[OCI (Open Container Initiative)](https://opencontainers.org/)** standards.

| OCI Specification | What It Defines |
|-------------------|-----------------|
| **Image Spec** | Image format, layers, manifests |
| **Runtime Spec** | How to run a container |
| **Distribution Spec** | How registries store/serve images |

**Result**: Same images, same commands, interchangeable tools.

---

## Common Commands Comparison

| Operation | Docker | Podman | OCI Standard |
|-----------|--------|--------|--------------|
| Run container | `docker run` | `podman run` | Runtime Spec |
| Build image | `docker build` | `podman build` | Image Spec |
| Pull image | `docker pull` | `podman pull` | Distribution Spec |
| Push image | `docker push` | `podman push` | Distribution Spec |
| List images | `docker images` | `podman images` | Image Spec |
| List containers | `docker ps` | `podman ps` | Runtime Spec |

---

## Container Lifecycle

| Command | Description |
|---------|-------------|
| `podman run` | Create and start container |
| `podman start` | Start stopped container |
| `podman stop` | Stop running container |
| `podman rm` | Remove container |
| `podman ps` | List containers |

```bash
# Identical to Docker
podman run -d -p 8080:80 --name web nginx
podman exec -it web bash
podman logs -f web
podman stop web && podman rm web
```

---

## Image Management

| Command | Description |
|---------|-------------|
| `podman pull` | Pull image from registry |
| `podman build` | Build image from Dockerfile |
| `podman push` | Push image to registry |
| `podman images` | List local images |
| `podman rmi` | Remove image |

```bash
# Build and push (same as Docker)
podman build -t myapp:latest .
podman tag myapp:latest quay.io/user/myapp:latest
podman push quay.io/user/myapp:latest
```

---

## Volume & Network

```bash
# Volumes - identical syntax
podman volume create mydata
podman run -v mydata:/data alpine

# Networks - identical syntax
podman network create mynet
podman run --network mynet alpine

# Bind mounts - identical syntax
podman run -v /host/path:/container/path alpine
```

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Podman Ecosystem

---

## Buildah

Build OCI images without a daemon.

| Feature | Description |
|---------|-------------|
| **From scratch** | Build without base image |
| **Dockerfile** | Full Dockerfile/Containerfile support |
| **Rootless** | Build images as regular user |
| **Scriptable** | Shell-friendly commands |

```bash
# Build from scratch
container=$(buildah from scratch)
buildah copy $container ./app /app
buildah config --cmd /app/run $container
buildah commit $container myimage
```

---

## Skopeo

Copy and inspect images without pulling.

| Feature | Description |
|---------|-------------|
| **Inspect** | View metadata without pulling |
| **Copy** | Transfer between registries |
| **Delete** | Remove from registries |
| **Sync** | Mirror repositories |

```bash
# Inspect without pulling
skopeo inspect docker://docker.io/library/nginx

# Copy between registries
skopeo copy docker://quay.io/image docker://ghcr.io/image

# Copy to local directory
skopeo copy docker://nginx dir:./nginx-image
```

---

## systemd Integration

Native systemd support for container management.

```bash
# Generate systemd unit file
podman generate systemd --name mycontainer \
  --files --new

# Enable as user service
systemctl --user enable container-mycontainer.service
systemctl --user start container-mycontainer.service

# Auto-start on boot (lingering)
loginctl enable-linger $USER
```

**Benefits**: Auto-restart, logging, dependency management

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Podman-Exclusive Features

---

## Native Pod Support

Pods group containers sharing namespaces (like Kubernetes).

```bash
# Create a pod with port mapping
podman pod create --name mypod -p 8080:80

# Add containers to pod
podman run -d --pod mypod --name web nginx
podman run -d --pod mypod --name cache redis

# Containers share localhost
# web can reach redis at 127.0.0.1:6379
```

| Shared | Not Shared |
|--------|------------|
| Network namespace | Filesystem |
| IPC namespace | Process namespace |
| Localhost | Environment |

---

## Kubernetes Integration

Generate and run Kubernetes YAML directly.

```bash
# Generate Kubernetes YAML from pod
podman generate kube mypod > mypod.yaml

# Run containers from Kubernetes YAML
podman play kube mypod.yaml

# Stop and remove
podman play kube --down mypod.yaml
```

**Use case**: Develop locally, deploy to Kubernetes with same YAML.

---

## podman-compose

Docker Compose compatibility for Podman.

```bash
# Install
pip install podman-compose

# Use existing docker-compose.yml
podman-compose up -d
podman-compose ps
podman-compose logs
podman-compose down
```

**Alternative**: Podman 4.1+ has built-in `podman compose`

```bash
podman compose up -d
```

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Practical Guidance

---

## Full Comparison Table

| Feature | Docker | Podman |
|---------|--------|--------|
| Daemon | Required | None |
| Root required | Default | No |
| CLI compatibility | - | 100% |
| Docker Compose | Native | podman-compose |
| Pods | No | Yes |
| Kubernetes YAML | No | Yes |
| systemd | Manual | Built-in |
| macOS/Windows | Docker Desktop | Podman Machine |
| GUI | Docker Desktop | Podman Desktop |
| Extensions | Docker Hub | Limited |

---

## When to Choose Podman

- **Security-first environments** - No root daemon
- **Multi-tenant systems** - User isolation
- **CI/CD pipelines** - No daemon to manage
- **Kubernetes migration** - Native pod/YAML support
- **systemd environments** - Native integration
- **RHEL/Fedora systems** - Default container engine

---

## When to Stay with Docker

- **Docker Desktop features** - Extensions, GUI tools
- **Existing Docker investment** - Swarm, enterprise features
- **Team familiarity** - Training considerations
- **Third-party integrations** - Some tools require Docker
- **Windows containers** - Better Windows support

---

## Migration Guide

**Step 1**: Install Podman
```bash
# Fedora/RHEL
dnf install podman

# Ubuntu/Debian
apt install podman

# macOS
brew install podman && podman machine init && podman machine start
```

**Step 2**: Set up compatibility
```bash
alias docker=podman
```

**Step 3**: Test existing workflows
```bash
podman-compose up -d  # Test compose files
podman build .        # Test Dockerfiles
```

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Conclusion

---

## Key Takeaways

| Docker | Podman |
|--------|--------|
| Daemon-based | Daemonless |
| Root by default | Rootless by default |
| Industry standard | Growing alternative |
| Rich ecosystem | Modular tools |

**Podman is not replacing Docker** - it's providing a secure, compatible alternative.

---

## Resources

**Official Documentation**
- [podman.io](https://podman.io)
- [docs.podman.io](https://docs.podman.io)

**Ecosystem**
- [buildah.io](https://buildah.io)
- [github.com/containers/skopeo](https://github.com/containers/skopeo)

**Desktop**
- [podman-desktop.io](https://podman-desktop.io)

**Community**
- [github.com/containers](https://github.com/containers)

---

<!-- .slide: data-background-color="#1a1a2e" -->

# Questions?

```bash
# Get started today
alias docker=podman
podman run hello-world
```
