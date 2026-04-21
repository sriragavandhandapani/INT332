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


---

# Module 8: Docker Compose, Persistence, and Real-World Fixes

## 1. Shifting Paradigms to Docker Compose

Deploying an application stack using raw `docker run` commands quickly spirals into chaos when managing dozens of arguments, environment variables, and network declarations. **Docker Compose** mitigates this by replacing imperative shell commands with declarative YAML tracking.

### A Modern Configuration Model
Instead of executing:
```bash
docker run -d -e RUN_ENV=production -p 8080:80 nginx
```

We transition to declarative files:

**`docker-compose.yml`**
```yaml
version: '3.8'
services:
  web_engine:
    image: nginx
    ports:
      - "${HOST_PORT}:80"
    environment:
      - RUN_ENV=${ENVIRONMENT_MODE}
```

**`.env`**
```env
ENVIRONMENT_MODE=production
HOST_PORT=8080
```

### Orchestration Execution
Start the entire multi-container stack in detached mode automatically:
```bash
docker-compose up -d
```

---

## 2. Navigating Common Pitfalls

### The "Exec" Syntax Trap
**The Mistake:** Reversing the command parameters.
```bash
docker exec -it bash <container_name> # Incorrect routing
```
**The Fix:** The command payload must be the final argument string.
```bash
docker exec -it <container_name> bash 
```

### Navigating Windows Host Paths
**The Mistake:** Using raw relative paths when mounting volumes or running `docker cp` in convoluted Windows environments (especially those utilizing OneDrive for Desktop synchronization).
**The Fix:** Always specify absolute, explicit pathing bounded in quotes if whitespace or heavy redirection is involved.
```bash
docker cp container_app:/logs.txt "C:\Users\admin\OneDrive\Desktop\"
```

### Resolving Naming Conflicts
**The Mistake:** Attempting to spawn a new container carrying the alias of a stopped container.
**The Fix:** Docker aliases are globally unique. Either prune old containers or enforce a force-remove workflow prior to standing up new instances:
```bash
docker rm -f specific_container_name
docker run ... --name specific_container_name ...
```

---

## 3. Process Interrogation Techniques

Before killing a container that seems unresponsive, you must triage.

**Linux Hosts:**
Analyze host-level binding to ensure the exposed port is genuinely listening:
```bash
ss -tuln
sudo lsof -i :8080
```

**Windows Hosts:**
```cmd
netstat -ano | findstr :8080
tasklist | findstr <PID_FROM_NETSTAT>
```

**The Universal Debug Routine:**
1. Assess process state: `docker ps -a`
2. Audit streaming telemetry: `docker logs -f <id>`
3. Interrogate internal logic: `docker exec -it <id> bash`
