# Week 1: Basics of DevOps Infrastructure (Up to cgroups)

## Topics Covered

* Introduction to DevOps
* Introduction to Containers
* Virtualization and Hypervisor
* Container Runtime
* Process Isolation & Namespaces
* Control Groups (cgroups)

---

## Introduction to DevOps

DevOps is a combination of Development (Dev) and Operations (Ops). It is a set of practices that enables collaboration between software development and IT operations teams.

Traditionally, development and operations worked separately:

* Developers focused on writing code
* Operations focused on deployment and maintenance

This separation caused delays, errors, and inefficiencies.

DevOps solves this by integrating both teams and promoting:

* Continuous Integration (CI) → Frequent code updates
* Continuous Delivery (CD) → Faster deployment

### Why DevOps is Needed

* Reduces communication gaps
* Speeds up development and deployment
* Improves reliability of applications
* Enables automation of repetitive tasks

---

## Introduction to Containers

### Origin of Containers

Initially, applications were deployed on physical servers where:

* One application used the entire system
* Resource utilization was poor

Then virtualization was introduced:

* Multiple Virtual Machines (VMs) run on one system

However, virtualization had limitations:

* Each VM requires its own OS
* High memory and CPU usage
* Slow startup time

To overcome this, containerization was introduced.

---

### Emergence of Modern Containerization

Containers are lightweight environments that:

* Share the host operating system
* Run applications in isolation
* Use fewer resources compared to VMs
* Start almost instantly

---

### Real-World Example (Used in Class)

* Independent House → Monolithic system
* Apartment → Virtual Machines
* PG System → Containers

Explanation:

* In a PG, resources like kitchen, electricity, and water are shared
* Each person has their own room (isolation)
* Similarly, containers share OS but isolate applications

---

## Virtualization and Hypervisor

Virtualization allows multiple virtual machines to run on a single physical system.

This is achieved using a **hypervisor**.

### Apartment Model Explanation

* Building → Physical Server
* Flats → Virtual Machines
* Manager → Hypervisor

---

### What is a Hypervisor?

A hypervisor is software that creates and manages virtual machines.

### Role of Hypervisor

* Allocates CPU, memory, and storage to each VM
* Ensures isolation between VMs
* Manages communication between hardware and VMs

### Limitation of Hypervisor-Based Systems

* Each VM requires a full OS
* Consumes more resources
* Slower compared to containers

---

## Container Runtime

A container runtime is responsible for running containers.

It acts as a bridge between the container and the operating system.

### Functions of Container Runtime

* Creates container environment
* Starts and stops containers
* Manages container lifecycle
* Applies isolation and resource limits

---

### Types of Container Runtime

#### High-Level Runtime

Examples:

* Docker Engine
* containerd
* CRI-O

Responsibilities:

* Image management
* Networking
* Storage

#### Low-Level Runtime

Example:

* runc

Responsibilities:

* Direct interaction with Linux kernel
* Uses namespaces and cgroups

---

## Process Isolation & Namespaces

Containers run independently due to process isolation.

This is achieved using **Linux namespaces**.

---

### What are Namespaces?

Namespaces are kernel features that isolate system resources for each container.

Each container gets its own view of:

* Processes
* Network
* Filesystem

---

### Types of Namespaces

#### PID Namespace (Process Isolation)

* Each container has its own process IDs
* Inside a container, processes start from PID 1
* Cannot see processes of other containers

---

#### Network Namespace

* Each container gets its own IP address
* Containers can use the same port independently
* Network is isolated

---

#### Mount Namespace

* Each container has its own filesystem
* Changes inside container do not affect host

---

### Key Idea

Namespaces provide **isolation**, but they do NOT control resource usage.

---

## Control Groups (cgroups)

Control Groups (cgroups) are used to limit and manage resource usage of containers.

---

### Why cgroups are Needed

Without cgroups:

* One container can consume all CPU or memory
* Other containers may become slow or crash
* System stability is affected

---

### What cgroups Control

* CPU usage → Limits processing power
* Memory usage → Prevents memory overflow
* Disk I/O → Controls read/write speed

---

### Example Scenario

If a container runs a heavy application:

* cgroups can limit it to use only 50% CPU
* Prevents it from affecting other containers

---

### Key Concept

* Namespaces → Provide isolation
* cgroups → Provide resource control

Both are essential for containerization.

---

## Summary

* DevOps improves collaboration and speeds up delivery
* Containers are lightweight alternatives to virtual machines
* Hypervisor enables virtualization but is resource-heavy
* Container runtime executes containers
* Namespaces isolate container environments
* cgroups control resource usage and prevent system overload
