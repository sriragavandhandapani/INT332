# Module 4: Advanced CLI, Variables, and Diagnostics

## 1. Environment Verification
Always ensure your installation is healthy and understand the layered history of the images you depend on.
```bash
docker --version         # Check CLI version
docker info              # Deep dive into engine details (runtimes, storage drivers)
docker history <image>   # Audit the layers of a specific image
```

## 2. Managing the Local Cache
Images are heavy. Proper management of what resides on your disk is critical.
```bash
docker images            # Audit local repository
docker pull httpd        # Manually cache an image from upstream
docker ps                # View healthy, running containers
docker ps -a             # Include ghost containers (stopped/crashed)
```

## 3. Dissecting execution (`docker run`)
The powerhouse command of Docker.
**Essential arguments:**
- `-d`: Background execution (Detached).
- `-p`: Network traffic routing.
- `--name`: Applies a semantic string alias instead of a random hash.
- `-it`: Bind to the container's interactive shell.

**Real-world usage:**
```bash
docker run -d -p 80:80 --name proxy_server nginx
```

## 4. Managing Configuration via Environment Variables
Applications behave differently across environments (Testing vs Prod). We control this behavior effortlessly through Environment Variables.

**Injecting single values:**
```bash
docker run -e DB_USER=admin ubuntu
```

**Injecting multiple values sequentially:**
```bash
docker run -e DB_USER=admin -e DB_PASS=secret ubuntu
```

**Mirroring a host variable:**
```bash
docker run -e PATH ubuntu
```

**Bulk injection (The correct way for massive configurations):**
Write a `.env` file containing configuration keys:
```env
ENVIRONMENT=staging
DEBUG_MODE=true
```
Then mount the file during execution:
```bash
docker run --env-file .env ubuntu
```

## 5. Runtime Control Vectors
Sometimes, you need to halt and resume workloads.
```bash
docker start <id>    # Wake up
docker stop <id>     # Graceful termination (SIGTERM)
docker restart <id>  # Cycle the container
docker pause <id>    # Freeze execution state via cgroups
```

## 6. General Diagnostics
```bash
docker exec -it <id> bash  # Pierce the namespace and get a shell
docker rm <id>             # Eradicate the container
docker rmi <id>            # Eradicate the image blueprint
```