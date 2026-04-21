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


---

# Module 10: Deep Inspection & Container Telemetry

Mastering container administration demands familiarity with how to analyze a container's health and configuration dynamically over its lifecycle.

---

## 1. Application Output Diagnostics (`docker logs`)

When an image terminates unexpectedly (Code 1 / 137), your immediate action must be log ingestion to track `stdout` / `stderr`.

**Execution Mechanics:**
```bash
docker logs <alias_or_id>
```

**Telemetry Augmentations:**
- `-f` / `--follow`: Stream data live. Mandatory for reactive debugging.
- `--tail X`: Limit output to the final `X` rows (ideal when combating verbose applications).
- `-t`: Emits strict system timestamps next to application outputs.
- `--since <time>`: Time-box the console dump (e.g., `--since 1h`).

---

## 2. Meta-State Assessment (`docker inspect`)

Log files capture the application's perspective, but `docker inspect` dumps the Engine's absolute truth about the container in parsed JSON format. Everything from explicit IP bindings to raw kernel mount paths is visible.

**Raw Extraction:**
```bash
docker inspect <alias_or_id>
```

**JSON Filtering via Go Templates:**
To avoid sifting through massive payloads, you can inject templating logic directly:
```bash
# Query the active runtime state boolean
docker inspect --format='{{.State.Running}}' target_node

# Output the precise file location storing the container logs physically on the host
docker inspect --format='{{.LogPath}}' target_node

# Decrypt the private IP address within a bound overlay/bridge network
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target_node
```

---

## 3. Real-Time Resource Utilization (`docker stats`)

`docker stats` acts identically to a localized `hwtop` daemon, explicitly scoped to the Docker architecture.

**Default Behavior:**
```bash
docker stats
```
Continually stream all operational containers in your terminal overlay.

**Monitored Resource Vectors:**
- **CPU %:** Absolute processor share.
- **MEM / LIMIT:** The exact bytes consumed versus the `cgroups` imposed cap.
- **NET I/O:** Socket bandwidth transfer rates.
- **BLOCK I/O:** Hard drive storage read/write volume.
- **PIDS:** Absolute number of independent processes birthed inside the sandbox.

**Scripted / Pipeline Usage:**
If polling metrics dynamically inside bash checks, enforce `-a --no-stream` to seize a static temporal frame and exit immediately.
