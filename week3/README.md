# Week 3: Docker Registry, Objects, Layering & Filesystem

## Topics Covered

* Docker Registry & Docker Hub
* Docker Object Types
* Docker Layering
* Docker Filesystem
* Docker Basic Commands

---

## Docker Registry & Docker Hub

A Docker registry is used to **store and distribute container images**.

Docker Hub is a **public registry** that provides:

* Official images
* Public/private repositories
* Easy sharing

---

## Docker Object Types

### Container

Running instance of an image.

### Image

Read-only blueprint for containers.

### Network

Enables communication between containers.

### Volume

Provides persistent storage.

---

## Docker Layering

* Each instruction creates a layer
* Layers are immutable, cached, reusable
* Improves build speed and storage efficiency

---

## Docker Filesystem

* Image → Read-only layers
* Container → Writable layer added
* Writable layer is temporary

---

# 🔥 Docker Commands (From PPT)

## 1. Image Commands

**Pull Ubuntu image**

```bash id="c1"
docker pull ubuntu
```

**List images**

```bash id="c2"
docker images
```

**Remove image**

```bash id="c3"
docker rmi ubuntu
```

---

## 2. Container Execution

**Run container**

```bash id="c4"
docker run ubuntu
```

**Run with terminal**

```bash id="c5"
docker run -it ubuntu /bin/bash
```

---

## 3. Container Lifecycle

**Stop container**

```bash id="c6"
docker stop <container_id>
```

**Remove container**

```bash id="c7"
docker rm <container_id>
```

---

## 4. Cleanup Commands

**Clean unused resources**

```bash id="c8"
docker system prune -a
```

**Full cleanup (Nuclear Clean ⚠️)**

```bash id="c9"
docker system prune -a --volumes
```

---

## 5. Nginx Container Commands (Important)

**Pull nginx image**

```bash id="c10"
docker pull nginx
```

**Run nginx container in background**

```bash id="c11"
docker run -d -p 8080:80 --name mynginx nginx
```

👉 `-d` → Detached mode
👉 `-p` → Port mapping (host:container)

---

**List running containers**

```bash id="c12"
docker ps
```

---

**Stop container**

```bash id="c13"
docker stop mynginx
```

---

**Start container**

```bash id="c14"
docker start mynginx
```

---

**Force remove container**

```bash id="c15"
docker rm -f mynginx
```

---

**Remove nginx image**

```bash id="c16"
docker rmi nginx
```

---

## 🔑 Important Notes

* `docker run` → creates + starts container
* `docker start` → starts existing container
* `-it` → interactive terminal
* `-d` → background mode
* `-p` → port mapping
* `prune` → removes unused resources

---

## Summary

* Docker Hub stores images
* Containers run applications
* Images act as blueprints
* Networks enable communication
* Volumes store persistent data
* Layering improves efficiency
* Commands manage full container lifecycle
