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
