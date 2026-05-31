# Lab Activities: Module 11 (GitHub Actions & Docker)

This laboratory module focuses on automating the container build lifecycle within GitHub Actions, establishing secure credential injection using Secrets, and pushing version-tagged images to multiple container registries.

---

## Challenge 1: Configure repository environment secrets
To prevent compromising your personal credentials, you will create a secure API access token on Docker Hub and store it inside your GitHub repository secrets.

### Steps:
1. Log in to your personal [Docker Hub](https://hub.docker.com/) account.
2. Go to **Account Settings** -> **Security** -> Click **New Access Token**.
3. Label the token `github-ci-token` with **Read, Write, Delete** permissions and generate it. Copy the generated token immediately (you will not be able to view it again!).
4. Go to your GitHub repository where INT332 is hosted.
5. Click **Settings** -> **Secrets and variables** -> **Actions**.
6. Click **New repository secret** and add:
   - **Name:** `DOCKER_HUB_USERNAME`
   - **Value:** *Your actual Docker Hub username*
7. Click **New repository secret** again and add:
   - **Name:** `DOCKER_HUB_PASSWORD`
   - **Value:** *The API Access Token you copied from Step 3*

---

## Challenge 2: Scaffold multi-registry build-and-push workflow
Construct a workflow that automatically compiles code, reads a Dockerfile, builds the container, and publishes the resulting package to Docker Hub and GHCR simultaneously.

### Steps:
1. Ensure your repository directory has a valid `Dockerfile` and a simple application context (you can use the multi-stage setup from Week 8).
2. Inside `.github/workflows/`, create a file named `publish-containers.yml` (for manual workflow template reference).
3. Insert the following code configuration:
   ```yaml
   name: Container Lifecycle Publisher
   
   on:
     push:
       branches: [ main ]
   
   jobs:
     build-and-push:
       runs-on: ubuntu-latest
       permissions:
         contents: read
         packages: write # Critical permission required to publish to GHCR
   
       steps:
         - name: Checkout Code
           uses: actions/checkout@v4
   
         - name: Setup Docker Buildx
           uses: docker/setup-buildx-action@v3
   
         - name: Login to Docker Hub
           uses: docker/login-action@v3
           with:
             username: ${{ secrets.DOCKER_HUB_USERNAME }}
             password: ${{ secrets.DOCKER_HUB_PASSWORD }}
   
         - name: Login to GitHub Container Registry (GHCR)
           uses: docker/login-action@v3
           with:
             registry: ghcr.io
             username: ${{ github.actor }}
             password: ${{ secrets.GITHUB_TOKEN }}
   
         - name: Generate Meta Tags
           id: meta
           uses: docker/metadata-action@v5
           with:
             images: |
               ${{ secrets.DOCKER_HUB_USERNAME }}/devops-lab
               ghcr.io/${{ github.repository }}
             tags: |
               type=raw,value=latest
               type=sha,format=short
   
         - name: Build & Publish Image
           uses: docker/build-push-action@v5
           with:
             context: .
             push: true
             tags: ${{ steps.meta.outputs.tags }}
             labels: ${{ steps.meta.outputs.labels }}
   ```

---

## Challenge 3: Validate registry delivery & tag immutable checks
Push code changes to trigger the automated build, verify the presence of the packages in both portals, and pull them locally to confirm functionality.

### Steps:
1. Force a change to your code, commit, and push:
   ```bash
   git add .
   git commit -m "Trigger automated container pipeline"
   ```
2. Open your GitHub Repository -> Go to **Actions** tab -> Monitor the `Container Lifecycle Publisher` workflow until it completes successfully.
3. **Verify Docker Hub:**
   - Log in to Docker Hub and go to your dashboard.
   - Verify that the image `devops-lab` exists, and has two tags: `latest` and a short commit SHA (e.g., `sha-d8f921a`).
4. **Verify GitHub Container Registry (GHCR):**
   - Go to your main GitHub profile or repository page.
   - Look under **Packages** on the right side.
   - You should see your repository listed as a Docker package container.
5. **Local Telemetry Check:**
   Tear down any local images and pull directly from your online repository to verify integrity:
   ```bash
   # Run from clean machine
   docker pull ghcr.io/<your-github-username>/int332/devops-lab:latest
   docker run --rm ghcr.io/<your-github-username>/int332/devops-lab:latest
   ```
