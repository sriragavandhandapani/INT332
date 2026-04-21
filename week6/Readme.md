# Module 6: Execution Mastery & Deep Diagnostics

## Core Curriculum
- The exact anatomy of `docker run`
- Detailed flag references
- Extensive Environmental control parameters
- Port forwarding mechanics
- Granular lifecycle logic
- Auditing through logs and inspects

---

## 1. Dissecting Execution

`docker run` is the linchpin command. It orchestrates pulling the image (if missing), provisioning an isolated filesystem, creating a network bridge, creating the container object, and initializing the primary process.

**The structure:**
`docker run [CONFIG FLAGS] IMAGE_URI [OVERRIDE COMMAND]`

**Primary Flags:**
- `-it`: Keeps STDIN connected for keyboard input.
- `-d`: Signals background orchestration.
- `--name`: Registers a DNS friendly alias.
- `--rm`: Commands Docker to wipe the container immediately upon process exit. Excellent for CI pipelines.
- `-p`: Maps `external:internal` networking.
- `-v`: Mounts storage schemas.
- `-e`: Declares environmental constants.

---

## 2. Using Configuration Constants

Hardcoding data like passwords or domain names into images is an anti-pattern. Instead, we use environment variables.

### The Problem
If a database URL is hardcoded, your image is locked to one environment. You'd have to build `image:dev`, `image:staging`, `image:prod`.

### The Solution
Use the same exact image, but change the `-e` flags passed at runtime to dynamically restructure the behavior.

**Examples:**
```bash
# Supplying multiple configs
docker run -e LOG_LEVEL=strict -e APP_REGION=us-east nginx
```

**Complex Workload Setup:**
```bash
docker run -d \
-e MYSQL_ROOT_PASSWORD=classified \
-e MYSQL_DATABASE=production_db \
-e MYSQL_USER=admin_service \
-e MYSQL_PASSWORD=backend_access \
mysql:8
```

---

## 3. Demystifying Port Forwarding

By default, an application listening on port 80 inside a container is completely inaccessible from the outside internet.

**The Solution:** You bind an open port on your host machine to route directly to the container's internal port using `-p HOST_BOUND:CONTAINER_LISTENER`.

**Example:**
`docker run -p 3000:80 nginx`
Visiting `localhost:3000` passes traffic straight to Nginx's port 80 proxy.

---

## 4. The Complete Lifecycle Spectrum

*   **Initialization:** `docker run ...`
*   **Waking up:** `docker start <alias>`
*   **Going to sleep:** `docker stop <alias>` (Sends SIGTERM wait)
*   **Immediate termination:** `docker kill <alias>` (Sends SIGKILL instantly)
*   **Cycling:** `docker restart <alias>`
*   **Freezing:** `docker pause <alias>` (Uses cgroups freezer mechanism)
*   **Unfreezing:** `docker unpause <alias>`
*   **Destruction:** `docker rm -f <alias>`

---

## 5. Advanced Auditing & Telemetry

When things fail, you must interrogate Docker.

### Process Identification
```bash
docker ps          # See what's breathing
docker ps -a       # Graveyard view included
```

### Deep Dive JSON Metrics
```bash
docker inspect <alias>
```
*Outputs a JSON block depicting everything: internal IP, mount structures, environment variables, and layer hashes.*

### Watch Performance Metrics
```bash
docker stats <alias>
```
*Shows a live `top`-like stream of CPU %, RAM allocation, and network TX/RX for the specific container.*

### Log Scrubbing
```bash
docker logs <alias>
docker logs -f <alias>    # Stream new incoming logs
```
