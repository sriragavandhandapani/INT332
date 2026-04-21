# Week 6: Docker Run, Environment Variables & Container Management

## Topics Covered

* Docker Run Command (In-depth)
* Docker Run Options
* Environment Variables in Docker
* Port Mapping (-p)
* Container Lifecycle Commands
* Container Inspection & Logs

---

## Docker Run Command

The `docker run` command is used to **create and start a container**.

### Syntax

```bash
docker run [OPTIONS] IMAGE [COMMAND]
```

---

### Parameters

* **OPTIONS** → Configure container (ports, env, name, etc.)
* **IMAGE** → Image name (nginx, ubuntu, etc.)
* **COMMAND** → Optional command to run inside container

---

## Important Docker Run Options

| Option   | Description           |
| -------- | --------------------- |
| `-it`    | Interactive mode      |
| `-d`     | Run in background     |
| `--name` | Assign container name |
| `--rm`   | Auto delete container |
| `-p`     | Port mapping          |
| `-e`     | Environment variables |
| `-v`     | Volume mount          |

---

## Examples

### Run simple container

```bash
docker run httpd echo "Hello, World!"
```

---

### Run container with name

```bash
docker run --name my-container httpd echo "Hello"
```

---

## Environment Variables

Environment variables are **key-value pairs** used to configure containers.

---

### Why needed?

Without `-e`:

* Hardcoded configuration
* Need to rebuild image

With `-e`:

* Dynamic configuration
* Flexible & reusable

---

### Example

```bash
docker run -e MY_VAR=value httpd env
```

---

### Multiple Variables

```bash
docker run -e APP_ENV=production -e APP_VERSION=1.0 nginx
```

---

### MySQL Example (Important)

```bash
docker run -d \
-e MYSQL_ROOT_PASSWORD=root123 \
-e MYSQL_DATABASE=college \
-e MYSQL_USER=admin \
-e MYSQL_PASSWORD=admin123 \
mysql:8
```

---

## Port Mapping (-p)

### Purpose

To expose container application to host.

---

### Syntax

```bash
docker run -p <HOST_PORT>:<CONTAINER_PORT> IMAGE
```

---

### Example

```bash
docker run -p 8080:80 nginx
```

👉 Browser → localhost:8080 → container:80

---

## Container Lifecycle Commands

### Run container

```bash
docker run -d -p 80:80 --name my_container nginx
```

---

### Start container

```bash
docker start <container_name>
```

---

### Stop container

```bash
docker stop <container_name>
```

---

### Restart container

```bash
docker restart <container_name>
```

---

### Pause / Unpause

```bash
docker pause <container_name>
docker unpause <container_name>
```

---

### Kill container

```bash
docker kill <container_name>
```

---

### Remove container

```bash
docker rm <container_name>
```

---

## Listing & Inspecting Containers

### Running containers

```bash
docker ps
```

---

### All containers

```bash
docker ps -a
```

---

### Inspect container

```bash
docker inspect <container_name>
```

---

### Resource usage

```bash
docker stats <container_name>
```

---

### Logs

```bash
docker logs <container_name>
docker logs -f <container_name>
```

---

## Key Notes

* `docker run` = create + start
* `-e` sets environment variables
* `-p` exposes container to browser
* logs are used for debugging
* containers are isolated environments

---

## Summary

* Docker run is core command
* Environment variables enable flexible configuration
* Port mapping connects container to host
* Lifecycle commands manage containers
* Logs and inspect help debugging
