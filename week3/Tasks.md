# Lab Activities: Module 3

This lab focuses on hands-on application of basic structural container operations.

---

## Exercise 1: Drilling into a Running Container
**Goal:** Prove that you can open a shell into a backgrounded application and monitor its diagnostics.

- **Drop into the bash shell:**
    ```bash
    docker exec -it <active_container_id> bash
    ```
- **Dump standard output/errors:**
    ```bash
    docker logs <active_container_id>
    ```
- **List real-time internal processes:**
    ```bash
    docker top <active_container_id>
    ```

---

## Exercise 2: Virtual Networking
**Goal:** Provision an isolated network bridge for arbitrary containers to communicate across.

- **Provision the network:**
    ```bash
    docker network create isolated-net
    ```
- **Verify creation:**
    ```bash
    docker network ls
    ```
- **Check network details (IP subnets, attached containers):**
    ```bash
    docker network inspect isolated-net
    ```

---

## Exercise 3: Booting Apache HTTPD
**Goal:** Stand up a functional static web server relying on port forwarding.

1.  **Retrieve HTTPD:**
    ```bash
    docker pull httpd
    ```
2.  **Verify local cache:**
    ```bash
    docker images
    ```
3.  **Deploy detached across Port 8081:**
    ```bash
    docker run -d --name my-apache-test -p 8081:80 httpd
    ```
4.  **Confirm process health:**
    ```bash
    docker ps
    ```
5.  **Test functionality:**
    Navigate your host system browser to `http://localhost:8081`.

---

## Exercise 4: Disposable Terminals
**Goal:** Spin up a completely ephemeral OS just to test some quick shell operations.

- **Launch Ubuntu interactively:**
    ```bash
    docker run -it ubuntu bash
    ```
- **Destroy the session:**
    ```bash
    exit
    ```