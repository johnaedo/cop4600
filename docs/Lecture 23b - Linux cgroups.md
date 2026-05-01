---
theme: css/dracula.css
width: "1920"
height: "1080"
share_cop4600: "true"
---

Linux **control groups** (_cgroups_) are a kernel feature that lets you **limit, isolate, and monitor** the resource usage of processes. They’re essential to modern container runtimes (Docker, Kubernetes), systemd, and many server environments.

Below is a clear explanation of:

1. **What cgroups are**
    
2. **How they work (architecture & mechanics)**
    
3. **How they are used in practice**
    

---

# 🧩 1. What cgroups are

**cgroups** (introduced by the Linux kernel in 2007) allow you to apply resource controls to a set of processes.  
They let you:

- **Limit** – e.g., cap CPU or memory usage
    
- **Prioritize** – give some processes more resources
    
- **Account** – measure how much CPU/memory I/O a group used
    
- **Isolate** – keep groups from interfering with each other
    
- **Freeze/thaw** – pause and resume groups of processes
    

They work by organizing processes into **hierarchies** where **controllers** enforce specific kinds of limits.

---

# ⚙️ 2. How cgroups work

## Overview of components:

### **• Cgroup hierarchy**

A _hierarchy_ is a tree of cgroups.  
Every process belongs to exactly one cgroup per hierarchy.

In practice, with cgroups v2, there is **one unified hierarchy**.

---

### **• Controllers**

Controllers enforce resource rules. Common controllers:

| Controller             | Purpose                           |
| ---------------------- | --------------------------------- |
| `cpu` / `cpuacct`      | Limit CPU usage / track CPU stats |
| `memory`               | Limit RAM, swap; send OOM events  |
| `blkio` or `io`        | Limit disk I/O throughput         |
| `pids`                 | Limit number of processes         |
| `cpuset`               | Bind processes to CPUs/NUMA nodes |
| `net_cls` / `net_prio` | Network tagging / priority        |
| `freezer`              | Freeze processes                  |

---
### **• Cgroup filesystem**

Mounted at something like:

```
/sys/fs/cgroup
```

Each cgroup is represented as a directory.  
Manipulating files inside the directory **configures** and **monitors** resources.

---
### **• How the kernel enforces limits**

When a controller applies a limit, the kernel checks the limit during resource allocation:

- For CPU, the scheduler throttles tasks that exceed their quota.
    
- For memory, if the group exceeds its limit, the kernel triggers cgroup-specific OOM handling.
    
- For I/O, access to block devices is queued proportionally.
    

---

# 🧱 2.1 Example: How a memory limit works

If you create a memory-limited cgroup:

```
echo 100M > /sys/fs/cgroup/mygroup/memory.max
echo <pid> > /sys/fs/cgroup/mygroup/cgroup.procs
```

Then the kernel:

- Tracks all memory allocations for processes in the group
    
- Denies new allocations when the limit is exceeded
    
- Sends an OOM event or kills processes in that cgroup
    

This enforcement is entirely done inside kernel subsystems.

---

# 🔀 2.2 cgroups v2

### **cgroups v2**

- Single unified hierarchy
    
- Cleaner API
    
- Better memory+I/O semantics
    
- Fully used by systemd and modern containers
    

Example directories (v2):

```
/sys/fs/cgroup/
  ├── user.slice/
  ├── system.slice/
  └── docker/
```

---

# 🛠️ 3. How cgroups are used

## 3.1 **Containers (Docker / Podman / Kubernetes)**

Containers rely heavily on cgroups:

- To **limit CPU/memory** (`--memory`, `--cpus`)
    
- To isolate processes per container
    
- To provide metrics for orchestration
    

---

### Example docker run:
- Docker writes these values into cgroup files automatically.
```bash
docker run --memory=256m --cpus=1 nginx
```




---

## 3.2 **systemd**

systemd creates a cgroup for:

- Each service (`/sys/fs/cgroup/system.slice/nginx.service`)
    
- Each user session
    
- The entire system tree hierarchy
    

Resource limits in `/etc/systemd/system/my.service` become cgroup settings:

```
[Service]
MemoryMax=500M
CPUQuota=50%
```

---

## 3.3 **Direct CLI usage (manual)**

You can manually create and use cgroups using:

- `systemd-run` (preferred on modern distros)
    
- Direct writing into `/sys/fs/cgroup`
    

---

## 3.4 **Kubernetes**

Pod resource requests and limits map directly to cgroup settings:

```
resources:
  limits:
    memory: "512Mi"
    cpu: "2"
```

The kubelet writes those settings into cgroup files for each pod container.

---

## 3.5 **High-performance systems / servers**

cgroups help:

- Guarantee resources for critical services
    
- Throttle batch jobs
    
- Collect per-job usage statistics
    

---

# 🧠 Summary

**Cgroups = kernel-level resource control frameworks.**  
They work by organizing processes into hierarchies where **controllers** enforce CPU, memory, and I/O limits.

**Used By:**

- Docker, Kubernetes, LXC
    
- systemd
    
- HPC clusters
    
- Direct CLI usage
    

They provide isolation and enforce strict limits—making them foundational to Linux containers.

---

If you'd like, I can also explain:

- cgroups v2 API and key files (`cgroup.procs`, `memory.max`, etc.)
    
- A step-by-step tutorial for creating your own cgroup
    
- How cgroups relate to namespaces (complete container explanation)