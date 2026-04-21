# Module 1: Foundations of DevOps & Containerization

## Core Concepts

- What is DevOps?
- Understanding Containers vs. VMs
- Hypervisors and Virtualization
- The Container Runtime Environment
- Linux Namespaces (Process Isolation)
- Control Groups (Resource Management)

---

## 1. Understanding DevOps

DevOps merges Software Development (Dev) and IT Operations (Ops). It forms a philosophy aiming to unify teams that historically worked in silos.

Previously:
- Developers strictly coded the applications.
- Operations managed server uptime, deployment, and infrastructure.

This disconnect led to slower release cycles and potential misalignments. DevOps bridges this gap by focusing on:
- **Continuous Integration (CI):** Automating code merging and testing.
- **Continuous Delivery (CD):** Streamlining the deployment process.

### Key Benefits
- Minimizes communication silos.
- Accelerates the release of features.
- Enhances system stability.
- Drives aggressive automation of manual tasks.

---

## 2. Exploring Containers

### The Evolution
Historically, apps ran natively on bare-metal servers. Since you could only run one primary stack effectively per machine, resource efficiency was low.

Virtualization changed this by allowing multiple Virtual Machines (VMs) on a single physical host. 
However, VMs present drawbacks:
- Each VM boots a hefty, independent Operating System.
- Startup times can be painfully slow.
- Substantial CPU and RAM overhead.

Containerization emerged as the modern solution.

### Modern Containers
Containers provide lightweight environments:
- They share the host's OS kernel rather than booting a new one.
- Applications run in strictly isolated units.
- They utilize minimal resources and spin up in milliseconds.

*Analogy:* If a bare-metal server is a standalone house and a VM is an apartment building with separate utilities, a Container is like a shared living space (PG) where everyone has a private room but shares the core utilities (the kernel).

---

## 3. Virtualization & The Hypervisor

Virtualization leverages a software layer to slice hardware into separate VMs.

### What is a Hypervisor?
A hypervisor (or Virtual Machine Monitor) manages these VMs. 
- It provisions CPU, RAM, and disk space for each VM.
- It enforces strict hardware-level isolation.
- It translates requests between the VM's OS and the actual physical hardware.

**The downside:** Running a full OS on top of a hypervisor is resource-intensive compared to the container approach.

---

## 4. Container Runtimes

The container runtime is the engine responsible for actually running the containers.

### Key Responsibilities
- Setting up the container execution environment.
- Governing the lifecycle (start, stop, pause) of a container.
- Enforcing resource constraints and isolation rules.

### Runtime Categories
**High-Level Runtimes:**
- Handle image distribution, networking, and high-level management.
- Examples: Docker Engine, containerd, CRI-O.

**Low-Level Runtimes:**
- Interact directly with the kernel to launch processes using namespaces and cgroups.
- Example: runc.

---

## 5. Linux Namespaces

Containers appear as independent systems thanks to Linux Namespaces. They hide the rest of the system from the containerized process.

### Primary Namespaces:
- **PID Namespace:** Isolates process IDs. The container believes it has a process with PID 1.
- **Network Namespace:** Provides an isolated network stack, including IP addresses, ports, and routing.
- **Mount Namespace:** Isolates the filesystem. The container gets its own root directory independent of the host.

*Note:* Namespaces dictate **what** a container can see, but they don't restrict **how much** of the system it can consume.

---

## 6. Control Groups (cgroups)

While Namespaces handle visibility, Control Groups (cgroups) handle consumption. They throttle and measure resource usage.

### Why do we need them?
If left unchecked, a single container could exhaust all the host's memory or CPU, causing a system-wide crash (Noisy Neighbor problem).

### What do they monitor?
- CPU usage
- RAM allocation
- Disk read/write speeds (Block I/O)

**Summary Paradigm:** 
*Namespaces = Isolation | cgroups = Allocation.* Both are mandatory for a true containerized ecosystem.
