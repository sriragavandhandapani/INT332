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
