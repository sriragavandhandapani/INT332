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
