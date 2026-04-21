# Docker Hands-on Tasks

This document contains a series of practical Docker tasks covering container management, networking, and deployment.

---

## Task 1: Troubleshooting a Running Container
**Objective:** Access a running container's shell and inspect its internal state.

* **Open bash inside container:**
    ```bash
    docker exec -it <container_id> bash
    ```
* **View logs:**
    ```bash
    docker logs <container_id>
    ```
* **Show running processes:**
    ```bash
    docker top <container_id>
    ```

---

## Task 2: Custom Networking
**Objective:** Create and manage a custom Docker network named `mynetwork`.

* **Create the network:**
    ```bash
    docker network create mynetwork
    ```
* **List all networks:**
    ```bash
    docker network ls
    ```
* **Inspect network configuration:**
    ```bash
    docker network inspect mynetwork
    ```

---

## Task 3: Deploying Apache Web Server (httpd)
**Objective:** Deploy a static website using the Apache HTTP Server in a detached container.

1.  **Pull the Apache image:**
    ```bash
    docker pull httpd
    ```
2.  **Verify the image download:**
    ```bash
    docker images
    ```
3.  **Run the container (Detached, Named, Port Mapped):**
    ```bash
    docker run -d --name apache-web -p 8081:80 httpd
    ```
4.  **Check running containers:**
    ```bash
    docker ps
    ```
5.  **Access the server:**
    Open your browser and navigate to `http://localhost:8081`.

---

## Task 4: Interactive Linux Practice
**Objective:** Use Docker to spin up an ephemeral Ubuntu environment for practicing Linux commands.

* **Run Ubuntu in interactive mode:**
    ```bash
    docker run -it ubuntu bash
    ```
* **Exit the container:**
    ```bash
    exit
    ```