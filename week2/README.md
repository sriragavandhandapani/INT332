# Module 2: Docker Ecosystem & Architectures

## Key Focus Areas
- Deep Dive into Images vs. Containers
- Storage and Distribution Registries
- The Docker Ecosystem
- Architectural Components
- Docker CLI Operations
- Knowledge Checks

---

## 1. Anatomy of a Container Image

Think of a container image as an immutable snapshot. It provides the exact blueprint for spinning up containers.

An image bundles everything required to execute your application:
- Source code
- Required software dependencies
- OS libraries
- Default configuration settings

*Crucial rule:* Once ran, this read-only template transitions into an active, writable entity known as a **container**.

---

## 2. Understanding Image Layers

Docker images are composed of discrete slices called **layers**. 

### How Layers Work
- Every command inside a `Dockerfile` (e.g., `RUN`, `COPY`) generates an independent layer.
- These layers stack sequentially. 
- They are entirely read-only (immutable).

### The Benefit of Layering
If you modify code at the very end of your build script, Docker only rebuilds that top layer while relying on the cache for all the base layers (like the OS and language runtimes). This results in:
- Rapid iteration and builds.
- Minimized disk footprint by sharing base layers across different images.

---

## 3. Registries & Distribution

A registry is a repository service where container images are published and fetched.

### Registry Varieties
- **Public:** Available to the global community (e.g., standard Docker Hub).
- **Private:** Gated, secure repositories for corporate/proprietary software.

### The Lifecycle Flow
1. Code is written and an image is built.
2. The image is `pushed` to the registry.
3. The registry archives it.
4. A host machine `pulls` the image.
5. A container is spawned from that pulled image.

---

## 4. Introducing Docker

Docker abstracts the gnarly details of interacting directly with Linux namespaces and cgroups. It provides a human-friendly toolset to build, ship, and manage standalone containers globally.

---

## 5. Docker's Internal Architecture

Docker heavily leverages a **Client-Server** methodology.

### The Client (Docker CLI)
This is the interface you type into. When you hit enter on `docker build` or `docker run`, the client communicates via a REST API to the server.

### The Server (Docker Daemon / dockerd)
The heavy lifter running in the background. It listens to the API and actively builds images, spins up containers, and mounts volumes.

### Registries
The external or internal hubs where the daemon looks when you request an image it doesn't possess locally.

### Images & Containers
- **Image:** The frozen blueprint.
- **Container:** The thawed, running execution of the image.

---

## 6. Navigating the CLI

The Docker Command Line Interface is your primary tool.

### Essential Commands
```bash
docker version      # Displays client/server versions
docker info         # Summarizes the engine's environment
docker pull nginx   # Fetches 'nginx' from the configured registry
docker run nginx    # Instantiates the fetched image
docker images       # Outputs locally cached images
docker ps           # Lists actively running containers
```

---

## 7. Concept Validations

### Scenario A
If a developer updates their application logic and rebuilds the Docker image, what actually gets rebuilt?
> **Solution:** Only the layer reflecting the code change and any subsequent layers below it. The cached foundational layers remain untouched, saving immense time.

### Scenario B
Which of the following statements about an image is false?
a) They are strictly read-only.
b) They come pre-packed with dependencies.
c) You can directly modify images on the fly.
d) They serve as the template for containers.
> **Solution:** **(c)** is incorrect. Images are immutable. Modifications require building a new image or editing a running container (which only changes its ephemeral thin layer).

### Scenario C
What is the primary function of Docker Hub?
a) Executing code.
b) Archiving and sharing container images.
c) Managing physical CPU thresholds.
d) Granting namespace isolation.
> **Solution:** **(b)** It is a registry service for distribution.

### Scenario D
Which piece of the architecture natively spins up containers?
a) The Docker Client
b) The Docker Daemon (dockerd)
c) Docker Hub
d) The Image
> **Solution:** **(b)** The Daemon handles all background execution.


---

# Module 9: Image Publication & GitHub Container Registry (GHCR)

## Introduction to GHCR

GitHub Container Registry (hosted at `ghcr.io`) serves as an elite alternative to Docker Hub natively unified into GitHub’s authentication and CI/CD pipelines. It is capable of hosting OCI-compliant artifacts directly alongside repository codebases.

---

## 1. Authentication Configuration

To interface securely with `ghcr.io` from your CLI workstation, you cannot utilize your standard GitHub password. An API authorization mechanism is required.

### Generating the PAT (Personal Access Token)
1. Within GitHub settings, navigate to **Developer Settings**.
2. Select **Personal access tokens (classic)** and generate a new key.
3. Grant it the `write:packages` and `delete:packages` permission scopes.
4. Export this token locally.

### Executing the Login
```bash
export GH_TOKEN="ghp_xxxxxxxxxxxxxxxxxxxxx"
echo $GH_TOKEN | docker login ghcr.io -u YOUR_GITHUB_HANDLE --password-stdin
```

---

## 2. Formulating a Custom Build

Let's prepare an asset that we can subsequently upload.

1. **Setup Directory:**
```bash
mkdir publish_test && cd publish_test
echo "<h1>Welcome to the GHCR Edge Node</h1>" > index.html
```

2. **The Dockerfile Blueprint:**
```dockerfile
# Utilizing lightweight Alpine Nginx
FROM nginx:alpine
# Inject our modified content
COPY index.html /usr/share/nginx/html/index.html
# Document intent to bind 80
EXPOSE 80
```

3. **Compiling locally:**
```bash
docker build -t prototype-web .
```

---

## 3. Remote Tagging Execution

A Docker image must mathematically define its remote destination inside its tag prior to any push action.

**The Strict Format Requirement:**
`ghcr.io/<GitHub-Username-Lowercase>/<Repository/Image-Title>:<Semantic-Version>`

**Action:**
```bash
# Tagging a stable version slice
docker tag prototype-web ghcr.io/your_handle/prototype-web:1.0.0

# Tagging the continuous latest slice
docker tag prototype-web ghcr.io/your_handle/prototype-web:latest
```

---

## 4. Transmission & Distribution

With the payload effectively stamped, transmission is straightforward:
```bash
docker push ghcr.io/your_handle/prototype-web:1.0.0
docker push ghcr.io/your_handle/prototype-web:latest
```

*Note on Privacy:* Artifacts uploaded to a personal account default to Private visibility. To shift them to public domain, you must alter the visibility status within the GitHub UI under the Package settings.

---

## 5. End-to-End Validation

To guarantee the image was successfully deployed and is publicly fetchable:

1. Obliterate your local cached references:
```bash
docker rmi prototype-web ghcr.io/your_handle/prototype-web:latest
```
2. Spawn an instance requesting the remote asset explicitly:
```bash
docker run -d -p 8080:80 --name ghcr-demo-node ghcr.io/your_handle/prototype-web:latest
```
3. Your localized container daemon automatically reaches out to GitHub servers to fulfill the read request.
