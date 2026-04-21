# Module 3: Storage, Objects, and Essential Commands

## Topics to Explore
- Docker Hub and Registry Ecosystems
- Core Docker Objects
- Deep Dive into Layering Principles
- Container Filesystem Architecture
- Essential CLI Syntax and execution patterns

---

## 1. Registry Mechanics

A registry acts as the central hub for hosting container images. **Docker Hub** serves as the default public cloud repository, granting access to officially verified images (like Node, Ubuntu, Nginx), enabling seamless global sharing, and providing private repo options for developers.

---

## 2. Fundamental Docker Objects

When working with Docker, you will interact with four primary object types:
- **Images:** The immutable, built templates.
- **Containers:** The instantiated, active runtime of an image.
- **Networks:** Virtual networks facilitating traffic between standalone containers.
- **Volumes:** Dedicated, persistent storage units that survive container deletions.

---

## 3. Storage Layering

Images utilize a Union Filesystem.
- Every directive in a build file adds a unique layer.
- Layers are heavily cached.
- Modifying your image optimally means putting frequently changed layers at the bottom.

### The Container's Filesystem Layer
When an image spins into a container, Docker slaps a **thin, writable layer** on top of the read-only image layers. Any file modifications made inside a running container are written here. When the container dies, this layer vanishes.

---

## 4. Master Command List

### Image Operations
```bash
# Download a base Ubuntu image
docker pull ubuntu

# View available local images
docker images

# Delete an image from local cache
docker rmi ubuntu
```

### Execution & Lifecycles
```bash
# Start an image in the foreground
docker run ubuntu

# Boot an image and drop into an interactive bash shell
docker run -it ubuntu /bin/bash

# Terminate a running container
docker stop <container_id>

# Erase a stopped container from the disk
docker rm <container_id>
```

### Essential Cleanup ⚠️
```bash
# Safely clear out dangling images and stopped containers
docker system prune -a

# Nuclear option: Obliterate everything unused, including persistent user volumes
docker system prune -a --volumes
```

### Practical Example: Nginx
```bash
# Fetch the web server image
docker pull nginx

# Launch in background (-d), bind host port 8080 to container port 80 (-p), and name it
docker run -d -p 8080:80 --name local_nginx nginx

# View active instances
docker ps

# Halt the web server
docker stop local_nginx

# Resume the server
docker start local_nginx

# Aggressively terminate and immediately destroy the container
docker rm -f local_nginx

# Clear the cached image
docker rmi nginx
```

### Quick Syntax Reminders
- `-it`: Keeps STDIN open and allocates a pseudo-TTY (interactive).
- `-d`: Detaches execution, returning the terminal prompt.
- `-p`: Forwards traffic (`host_port:container_port`).
- `docker run` encompasses an implicit `docker pull`, `docker create`, and `docker start`.
