# Module 5: Orchestrating Data & Real Deployments

## Main Subjects
- Host-to-Container interaction protocols (`exec`, `cp`)
- The philosophy of persistent Volumes
- Injecting state via variables
- Comprehensive debugging patterns

---

## 1. Bridging the Host and Container

Occasionally, it is necessary to manually tweak files or run secondary processes in an active container.

### Running Secondary Processes
```bash
# Spawning a secondary bash session inside a living container
docker exec -it <id> /bin/bash
```

### Connecting to the Primary Process
```bash
# Attach your terminal to the container's PID 1 standard output
docker attach <id>
```

### Physical File Migration
Moving data in and out manually using the `cp` command syntax.
**Outward bound (Extracting data):**
```bash
docker cp <container_id>:/var/log/app.log ~/Desktop/app.log
```
**Inward bound (Injecting data):**
```bash
docker cp ~/Desktop/config.json <container_id>:/etc/app/config.json
```

---

## 2. Emulating File Actions (Internal)
If you `exec` into the container, you can manipulate its filesystem using basic POSIX tools:
```bash
mkdir /persistence
echo "Starting system..." > /persistence/init.txt
cat /persistence/init.txt
```

---

## 3. The Power of Volumes

Containers are ephemeral. If they vanish, their modified filesystem vanishes. **Volumes** are Docker-managed folders on the host that are mounted securely into the container to ensure data survives destruction.

### Volume Operations
```bash
docker volume create app_data        # Provision a new volume
docker volume ls                     # Audit all provisions
docker volume inspect app_data       # Get physical mount path details
docker volume rm app_data            # Delete the specified provisioning
docker volume prune                  # Wipe out all unattached volumes logically
```

### Attaching Volumes
You mount a volume using the `-v` flag (`volume_name:internal_target_path`).
```bash
docker run -dit --name app-worker -v app_data:/var/lib/data ubuntu
```

---

## 4. Complex Deployments

### Stateful Web Deployment
A full deployment linking ports, environment variables, and persistent storage:
```bash
docker run -dit --name core-web -p 8080:80 -e OPERATION_MODE=prod -v web_assets:/usr/local/apache2/htdocs httpd
```

### Advanced Database Deployment
A MySQL instance requires extensive configuration passed via the `-e` flag to configure users securely upon bootstrap:
```bash
docker run -d --name sql_engine -p 3307:3306 \
  -e MYSQL_ROOT_PASSWORD=supersecure \
  -e MYSQL_DATABASE=student_db \
  -e MYSQL_USER=app_user \
  -e MYSQL_PASSWORD=app_pass mysql:8
```

---

## 5. Telemetry and Logs
If a deployment fails, parsing the console logs emitted by the main process is critical.
```bash
docker logs sql_engine         # Dump the log history
docker logs -f sql_engine      # Follow the log stream continuously
```
