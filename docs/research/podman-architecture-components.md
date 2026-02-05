# Podman Architecture Components

Podman is a daemonless, open-source container engine. This document describes each component in the Podman architecture.

## Client

### Podman CLI

The command-line interface for interacting with containers. Compatible with Docker CLI commands.

| Command | Description |
|---------|-------------|
| `podman run` | Create and run a container |
| `podman build` | Build an image from Containerfile/Dockerfile |
| `podman pull` | Pull an image from a registry |

**Key difference from Docker:** Podman CLI communicates directly with the container runtime - no daemon intermediary.

---

## Podman Host

### Podman Process

- **Daemonless** - No background service required
- **Fork/exec model** - Each container spawns as a child process
- **Rootless by default** - Runs with user privileges, not root
- **Direct execution** - CLI forks directly to conmon

### conmon (Container Monitor)

A small C program that monitors container processes.

| Feature | Description |
|---------|-------------|
| **Purpose** | Monitor container lifecycle |
| **Persistence** | Stays alive after Podman exits |
| **Logging** | Handles container stdout/stderr |
| **PTY** | Manages pseudo-terminals for interactive containers |

**Why it exists:** Allows the Podman process to exit while containers keep running.

### OCI Runtime

The low-level container runtime that creates containers.

| Runtime | Description |
|---------|-------------|
| **runc** | Reference implementation, written in Go |
| **crun** | Faster alternative, written in C (default on Fedora/RHEL) |
| **runv** | VM-based runtime for stronger isolation |

**OCI Specification:** Defines how to run a "filesystem bundle" as a container.

### Images (Local Storage)

Container images stored locally in the user's home directory.

| Location | Description |
|----------|-------------|
| `~/.local/share/containers/storage/` | Rootless storage |
| `/var/lib/containers/storage/` | Root storage |

**Format:** OCI image format (compatible with Docker images)

### Containers (Rootless)

Running container instances with user-level privileges.

| Feature | Description |
|---------|-------------|
| **User namespaces** | Map container root to unprivileged user |
| **No root required** | Enhanced security |
| **Reduced capabilities** | 11 capabilities vs Docker's 14 |
| **Isolation** | Each container is a child process |

---

## Registry & Tools

### Container Registries

Podman supports multiple OCI-compliant registries.

| Registry | URL | Description |
|----------|-----|-------------|
| **Quay.io** | quay.io | Red Hat's container registry |
| **Docker Hub** | docker.io | Largest public registry |
| **GitHub Container Registry** | ghcr.io | GitHub's registry |
| **Red Hat Registry** | registry.redhat.io | Enterprise images |

### Buildah

A tool for building OCI container images.

| Feature | Description |
|---------|-------------|
| **From scratch** | Build images without a base |
| **No daemon** | Daemonless like Podman |
| **Dockerfile compatible** | Supports Dockerfile/Containerfile |
| **Rootless** | Build images without root |

**Command example:**
```bash
buildah from scratch
buildah copy mycontainer ./app /app
buildah commit mycontainer myimage
```

### Skopeo

A tool for copying and inspecting container images.

| Feature | Description |
|---------|-------------|
| **Copy images** | Between registries, local storage, tarballs |
| **Inspect** | View image metadata without pulling |
| **Delete** | Remove images from registries |
| **Sync** | Synchronize repositories |

**Command examples:**
```bash
skopeo inspect docker://docker.io/library/nginx
skopeo copy docker://quay.io/myimage docker://docker.io/myimage
```

### systemd Integration

Podman integrates with systemd for service management.

| Feature | Description |
|---------|-------------|
| **Generate units** | `podman generate systemd` creates unit files |
| **Socket activation** | Start containers on-demand |
| **User services** | Run as user systemd services |
| **Auto-restart** | Automatic container restart on failure |

**Command example:**
```bash
podman generate systemd --name mycontainer > ~/.config/systemd/user/mycontainer.service
systemctl --user enable --now mycontainer
```

---

## Architecture Comparison

| Aspect | Docker | Podman |
|--------|--------|--------|
| **Daemon** | dockerd (required) | None (daemonless) |
| **Root** | Root by default | Rootless by default |
| **Process model** | Client-server | Fork/exec |
| **Container monitor** | containerd | conmon |
| **Build tool** | Built-in | Buildah (modular) |
| **Image transfer** | Built-in | Skopeo (modular) |

## Sources

- [Podman Documentation](https://docs.podman.io/)
- [Red Hat - What is Podman?](https://www.redhat.com/en/topics/containers/what-is-podman)
- [Buildah Documentation](https://buildah.io/)
- [Skopeo GitHub](https://github.com/containers/skopeo)
