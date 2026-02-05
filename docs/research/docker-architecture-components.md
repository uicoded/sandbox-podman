# Docker Architecture Components

Docker is a daemon-based, open-source container platform. This document describes each component in the Docker architecture.

## Client

### Docker CLI

The command-line interface for interacting with containers. Communicates with the Docker daemon via REST API.

| Command | Description |
|---------|-------------|
| `docker run` | Create and run a container |
| `docker build` | Build an image from Dockerfile |
| `docker pull` | Pull an image from a registry |

**Client-Server Model:** Docker CLI sends commands to the Docker daemon (dockerd) over a Unix socket (`/var/run/docker.sock`) or TCP.

---

## Docker Host

### Docker Daemon (dockerd)

The central background service that manages all Docker objects.

| Feature | Description |
|---------|-------------|
| **Always running** | Background service must be active |
| **Root privileges** | Requires root by default |
| **REST API** | Listens for API requests |
| **Container lifecycle** | Creates, starts, stops, removes containers |

**Architecture:** The daemon handles building, running, and distributing containers. All CLI commands go through the daemon.

### containerd

A container runtime that manages the complete container lifecycle.

| Feature | Description |
|---------|-------------|
| **Purpose** | High-level container runtime |
| **Image management** | Push/pull images, manage storage |
| **Container execution** | Delegates to runc for actual execution |
| **Supervision** | Monitors running containers |

**Why it exists:** Provides a layer of abstraction between Docker daemon and the OCI runtime.

### runc

The low-level OCI runtime that creates and runs containers.

| Feature | Description |
|---------|-------------|
| **OCI compliant** | Implements OCI runtime specification |
| **Spawns containers** | Creates the actual container process |
| **Lightweight** | Exits after container starts |

### Images (Local Storage)

Container images stored locally on the Docker host.

| Location | Description |
|----------|-------------|
| `/var/lib/docker/` | Default storage location |
| `overlay2/` | Image layers (default storage driver) |
| `image/` | Image metadata |

**Format:** OCI image format with layered filesystem.

### Containers

Running container instances managed by the daemon.

| Feature | Description |
|---------|-------------|
| **Isolated processes** | Namespaces for isolation |
| **Layered filesystem** | Union mount with copy-on-write |
| **Networking** | Bridge network by default |
| **14 capabilities** | Linux capabilities for security |

---

## Registry & Tools

### Container Registries

Docker supports OCI-compliant registries for storing and distributing images.

| Registry | URL | Description |
|----------|-----|-------------|
| **Docker Hub** | docker.io | Default public registry |
| **GitHub Container Registry** | ghcr.io | GitHub's registry |
| **Quay.io** | quay.io | Red Hat's container registry |
| **Amazon ECR** | *.amazonaws.com | AWS container registry |

### Extensions (Docker Desktop)

Docker Desktop extensions enhance functionality through a GUI marketplace.

| Extension | Description |
|-----------|-------------|
| **Portainer** | Container management GUI |
| **Trivy** | Security vulnerability scanner |
| **Snyk** | Developer security platform |
| **Lens** | Kubernetes cluster access |
| **PGAdmin4** | PostgreSQL administration |

**Note:** Extensions are specific to Docker Desktop, not available in Docker Engine alone.

### Plugins

Docker Engine plugins extend functionality for networking, storage, and authorization.

| Type | Purpose | Examples |
|------|---------|----------|
| **Volume** | Persistent/distributed storage | Flocker, REX-Ray |
| **Network** | Custom networking (VLAN, overlay) | Weave, Calico |
| **Authorization** | Granular access control | Open Policy Agent |

**Plugin commands:**
```bash
docker plugin install <plugin>
docker plugin ls
docker plugin enable <plugin>
```

---

## Architecture Comparison

| Aspect | Docker | Podman |
|--------|--------|--------|
| **Daemon** | dockerd (required) | None (daemonless) |
| **Root** | Root by default | Rootless by default |
| **Process model** | Client-server | Fork/exec |
| **Container runtime** | containerd + runc | conmon + OCI runtime |
| **Build tool** | Built-in | Buildah (modular) |
| **Image transfer** | Built-in | Skopeo (modular) |
| **Socket** | /var/run/docker.sock | Per-user socket |

## Sources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Architecture Overview](https://docs.docker.com/get-started/docker-overview/)
- [containerd](https://containerd.io/)
- [Open Container Initiative](https://opencontainers.org/)
