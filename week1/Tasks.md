# Week 1: Tasks (From PPT)

---

## 🔹 Task 1

### Scenario

A company uses containers for microservices. One faulty container crashes the host due to high memory usage.

---

### Questions & Answers

**1. Which container feature was misconfigured or missing?**
👉 Resource limits were not set properly (cgroups misconfiguration).
The container was allowed to use unlimited memory, leading to system crash.

---

**2. Which kernel mechanism should have prevented this?**
👉 Control Groups (cgroups).
cgroups limit resource usage such as memory and prevent containers from overusing system resources.

---

**3. Would namespaces alone solve this issue? Why?**
👉 No, namespaces alone cannot solve this issue.

Reason:

* Namespaces provide isolation (process, network, filesystem)
* But they do NOT control resource usage

👉 Hence, cgroups are required for resource control.

---

## 🔹 Task 2

### Scenario

A server is running three containers:

* Container A → CPU-intensive task
* Container B → Web server
* Container C → Database

---

### Questions & Answers

**1. Which Linux feature prevents Container A from consuming all CPU?**
👉 Control Groups (cgroups).
cgroups limit CPU usage and ensure fair resource distribution among containers.

---

**2. Which feature ensures Container B cannot see processes of Container C?**
👉 PID Namespace.
Each container has its own process space, so processes are isolated.

---

**3. Which namespace allows all three containers to use port 80 internally?**
👉 Network Namespace.
Each container has its own network stack, allowing reuse of the same port internally.

---

## 🔥 Final Conclusion

👉 Namespaces provide isolation, while cgroups provide resource control. Both are essential for stable container environments.
