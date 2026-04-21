# Week 2: Container Images, Docker & Architecture

## Topics Covered

* Container Images & Layers
* Image Registries & Distribution
* Introduction to Docker
* Docker Architecture
* Docker Daemon
* Docker CLI
* Tasks / MCQs (from PPT)

---

## Container Images

A container image is a **read-only template** used to create containers.

It contains:

* Application code
* Dependencies
* Libraries
* Runtime environment

👉 When we run an image, it becomes a **container (running instance)**.

---

## Image Layers

Container images are built using **layers**.

### What are Layers?

* Each instruction in a Dockerfile creates a new layer
* Layers are stacked on top of each other
* Each layer is **immutable (cannot be changed)**

---

### Example

If a Dockerfile contains:

* Install OS
* Install Node.js
* Copy application code

👉 Each step becomes a separate layer

---

### Advantages of Layers

* Faster builds (cached layers are reused)
* Reduced storage (shared layers)
* Efficient updates (only changed layers are rebuilt)

---

## Image Registries & Distribution

An image registry is a storage system used to store and share container images.

---

### Types of Registries

#### Public Registry

* Accessible to everyone
* Example: Docker Hub

#### Private Registry

* Restricted access
* Used by organizations

---

### Image Distribution Process

1. Developer builds an image
2. Image is pushed to registry
3. Stored in registry
4. Pulled by another system
5. Container is created

👉 This enables portability across environments.

---

## Introduction to Docker

Docker is a platform that simplifies:

* Building images
* Running containers
* Managing containerized applications

It provides an easy interface to use containerization without dealing directly with low-level OS features.

---

## Docker Architecture

Docker follows a **client-server architecture**.

---

### Components

#### Docker Client

* Interface used by users
* Executes commands like:

  * docker run
  * docker build

---

#### Docker Daemon

* Runs in the background
* Responsible for:

  * Managing containers
  * Handling images
  * Managing networks and volumes

---

#### Docker Registry (Docker Hub)

* Stores Docker images
* Allows push and pull operations

---

#### Docker Images

* Blueprint for containers
* Read-only

---

#### Containers

* Running instance of an image
* Includes writable layer

---

## Docker Daemon (In-depth)

The Docker daemon is the **core engine** of Docker.

### Responsibilities

* Builds images
* Runs containers
* Manages lifecycle
* Handles communication with registry

👉 It listens to Docker API requests and executes them.

---

## Docker CLI

Docker CLI (Command Line Interface) is used to interact with Docker.

### Common Commands (Basic)

```bash
docker version
docker info
docker pull nginx
docker run nginx
docker images
docker ps
```

---

### Command Explanation

* docker pull → Downloads image from registry
* docker run → Creates and starts container
* docker images → Lists images
* docker ps → Shows running containers

---

## Tasks / MCQs (From PPT)

### Task 1

A developer modifies a Dockerfile and rebuilds the image.

**Question:**
Which layers will be rebuilt?

**Answer:**
👉 Only the modified layer and the layers after it will be rebuilt due to caching.

---

### Task 2

Which of the following is NOT true about container images?

a) Images are read-only
b) Images contain application dependencies
c) Images can be modified directly
d) Images are used to create containers

**Answer:**
👉 c) Images can be modified directly

---

### Task 3

What is the role of Docker Registry?

a) Run containers
b) Store and distribute images
c) Manage CPU usage
d) Provide process isolation

**Answer:**
👉 b) Store and distribute images

---

### Task 4

Which component is responsible for running containers?

a) Docker CLI
b) Docker Daemon
c) Docker Hub
d) Image

**Answer:**
👉 b) Docker Daemon

---

### Task 5

Which command is used to download an image?

a) docker run
b) docker pull
c) docker start
d) docker build

**Answer:**
👉 b) docker pull

---

## Summary

* Container images are read-only templates
* Layers improve efficiency and performance
* Registries store and distribute images
* Docker simplifies container usage
* Docker architecture includes client, daemon, and registry
* CLI is used to interact with Docker
* Tasks help understand real-world scenarios
