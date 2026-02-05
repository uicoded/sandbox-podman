# Docker Registry Components

Docker Hub's content library features three product categories: **Images**, **Extensions**, and **Plugins**.

## Images

Container images are the core of Docker Hub. These include OS images, databases, and language runtimes.

**Most Popular Official Images:**

| Image | Description | Use Case |
|-------|-------------|----------|
| nginx | Web server and reverse proxy | Web serving, load balancing |
| postgres | PostgreSQL database | Relational database |
| ubuntu | Ubuntu Linux base image | General-purpose base |
| alpine | Minimal Linux (~5MB) | Lightweight containers |
| mysql | MySQL database | Relational database |

## Extensions

Docker Desktop extensions enhance the core functionality of Docker Desktop.

**Most Popular Extensions:**

| Extension | Description |
|-----------|-------------|
| Portainer | Container management GUI |
| Trivy | Security vulnerability scanner |
| Snyk | Developer security platform |
| Lens | Kubernetes cluster access |
| PGAdmin4 | PostgreSQL administration |

## Plugins

Docker Engine plugins extend functionality for networking, storage, and authorization.

**Plugin Categories:**

| Type | Purpose | Example |
|------|---------|---------|
| Volume | Persistent/distributed storage | Flocker, REX-Ray |
| Network | Custom networking (VLAN, bridges) | Weave, Calico |
| Authorization | Granular access control | Open Policy Agent |

## Sources

- [Docker Hub](https://hub.docker.com/)
- [Docker Extensions](https://www.docker.com/products/extensions/)
- [Docker Engine Plugins](https://docs.docker.com/engine/extend/)
