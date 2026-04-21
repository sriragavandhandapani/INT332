# Week 1 Lab: Hands-on Task Scenarios

---

## 📌 Task 1: Resource Exhaustion

### Scenario Description
A development team has deployed a fleet of microservices. Suddenly, one specific container begins allocating massive amounts of RAM, eventually causing the entire host machine to freeze and crash. 

### Assessment
**1. What architectural feature failed or was omitted here?**  
> Hard resource constraints were omitted. The container was granted unmetered access to the host's memory pool, leading to total starvation.

**2. Which underlying Linux kernel feature is responsible for preventing this scenario?**  
> Control Groups (cgroups). They establish strict ceilings on how much CPU or memory a group of processes can utilize.

**3. Are Linux Namespaces sufficient to prevent this?**  
> No. Namespaces segregate visibility (e.g., hiding other processes or networking stacks), but they do not enforce consumption limits. cgroups handle the throttling.

---

## 📌 Task 2: Service Isolation

### Scenario Description
A single Linux host runs three distinct containers:
- **App A:** A heavy data-processing application.
- **App B:** A frontend web server.
- **App C:** A backend SQL database.

### Assessment
**1. How does the system keep App A from hoarding all the processor cycles?**  
> Through **cgroups**, which cap the percentage of CPU time App A can claim.

**2. Why is App B fundamentally blind to the running processes of App C?**  
> Because they reside in different **PID Namespaces**. Each container has its own isolated process tree starting at PID 1.

**3. How is it possible for all three distinct applications to internally bind to Port 80 without conflicting?**  
> They each inhabit their own isolated **Network Namespace**. This provides a dedicated networking stack per container, allowing internal IP and port structures to overlap safely across containers.
