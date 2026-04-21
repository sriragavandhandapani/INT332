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


---

# Module 7: Advanced Networking Frameworks

## The Need for Docker Networking

Networking in Docker isn't just about accessing the internet; it is the fundamental backbone for how isolated containers speak to each other securely and how services discover one another without hardcoded IPs.

---

## 1. Core Network Drivers

Docker utilizes several core network drivers depending on the security and topological needs of your architecture.

### The Bridge Network (Default)
When a container launches without explicit network instructions, it lands on the `bridge` driver. 
- **Characteristics:** It isolates traffic from outside containers but allows intra-bridge communication.
- **The Catch:** Containers on the *default* bridge can only find each other via IP address, which is impractical for dynamic scaling.
- **The Solution:** Always provision a **User-Defined Bridge**. These custom bridges inject an automatic DNS resolver so containers can simply ping each other by their `--name`.

### The Host Network
Removes the networking sandbox. The container shares the host machine’s TCP/IP stack 1:1.
- **Characteristics:** If an Nginx container spins up on port 80, it instantly consumes the host server's port 80. You no longer need `-p 8080:80`.
- **Use Case:** High-throughput performance scenarios where routing overhead must be avoided. *(Note: Linux native only).*

### The Overlay Network
Built for distributed clusters like Docker Swarm.
- **Characteristics:** Connects multiple distinct host machines across a VXLAN, deceiving the containers into believing they are on the same local subnet despite spanning geographic regions.

### Macvlan & None
- **Macvlan:** Binds directly to physical network interfaces, appearing as a pure physical device on the switch.
- **None:** Absolute network isolation. Loopback only.

---

## 2. Inner Working of Docker DNS

Service discovery is paramount in microservice architectures. 

1. Every container routes its DNS queries to an embedded resolver located at `127.0.0.11`.
2. Rather than querying standard nameservers first, Docker checks its internal ledger. If container `frontend-app` wants to talk to `redis-cache` (and both are on a user-defined network), `127.0.0.11` intercepts the request and instantly translates `redis-cache` to its internal IP.
3. If the request is external (like `api.github.com`), Docker forwards the query out to the host's standard DNS upstream (like Google's `8.8.8.8`).

---

## 3. Practical Networking Tasks

### Establishing a Custom Bridge
```bash
# Provisioning a named bridge
docker network create internal-services

# Auditing network definitions
docker network ls
docker network inspect internal-services
```

### Proving DNS Connectivity
```bash
# Launch a background service
docker run -d --name cache-db --network internal-services nginx:alpine

# Launch an adjacent container and test DNS resolution
docker run -it --name api-server --network internal-services nginx:alpine bash
```

Inside the terminal of `api-server`, issue a ping using the target's semantic name:
```bash
ping -c 4 cache-db
```
*Outcome:* The ping flawlessly resolves the target container's ephemeral IP.

### Operational Cleanup
```bash
docker network disconnect internal-services api-server
docker network rm internal-services
docker network prune
```
