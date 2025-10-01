Got it! You want a clear explanation of **roles in Bright Cluster Manager (BCM)** and specifically the **DockerHost role**, plus how to use them. Let’s break it down clearly and step by step.

---

### **1. What are Roles in BCM?**

* A **role** in BCM defines a **set of configurations and services** that can be applied to nodes or categories of nodes.
* Roles allow you to **centralize management** of specific services or features across multiple nodes.
* Examples:

  * `Docker::Host` → manages Docker service
  * `NFS::Server` → manages NFS service
  * `Compute::Node` → standard compute node role

**Key idea:** Instead of configuring each node individually, you assign a role and BCM applies the configuration automatically.

---

### **2. How Roles Work**

* Roles can be **assigned to a node** or **a category of nodes**.
* Each role has **parameters** that can be customized (like a template for the service).
* Changes to a role **propagate** to all nodes that have the role assigned.
* BCM handles **provisioning, service management, and configuration** for that role.

---

### **3. DockerHost Role**

The **DockerHost role** is used for nodes that run Docker.

**Responsibilities:**

* Installs Docker on assigned nodes.
* Manages Docker configuration and service.
* Ensures Docker daemon runs with the specified parameters.
* Handles Docker storage (`spool`), logging, networking, SELinux, and API sockets.

**Key Parameters (from your example):**

| Parameter                 | Description                                       |
| ------------------------- | ------------------------------------------------- |
| Add services              | Yes → automatically starts Docker service         |
| Name                      | Role name (`Docker::Host`)                        |
| Provisioning associations | Internal BCM associations                         |
| Enable SELinux            | Yes → SELinux enforced for Docker                 |
| Log Level                 | Docker logging level (`info`)                     |
| Spool                     | Docker storage path (`/var/lib/docker`)           |
| Tmp dir                   | Temporary directory for Docker (`$spool/tmp`)     |
| API Sockets               | Docker API socket (`unix:///var/run/docker.sock`) |
| Bridge / Bridge IP        | Network bridge for containers                     |
| Iptables                  | Yes → Docker manages iptables rules               |
| MTU                       | Network MTU (0 = default)                         |
| User Namespace Remap      | User namespace mapping for containers             |
| Insecure Registries       | List of insecure Docker registries                |
| Certificates Path         | Path for TLS certificates (`/etc/docker`)         |

---

### **4. Assigning a Role via `cmsh`**

**Step-by-step:**

1. Enter cmsh:

```bash
cmsh
```

2. Select a **category** or node:

```bash
category use default
# or
device use node001
```

3. List current roles:

```bash
roles
```

4. **Assign DockerHost role**:

```bash
assign docker::host
```

5. Verify role parameters:

```bash
show
```

6. **Commit changes** (applies configuration):

```bash
commit
```

> After committing, BCM will automatically configure Docker on all nodes assigned with this role.

---

### **5. Managing Role Parameters**

* You can **customize role parameters** before committing:

```bash
set loglevel=debug
set spool=/mnt/docker
```

* Then `commit` to apply.
* Any changes propagate automatically to all nodes with that role.

---

### **6. Benefits of Using Roles**

* Centralized configuration.
* Consistent service setup across nodes.
* Easy updates: modify role → changes applied to all assigned nodes.
* Reduces manual errors and management overhead.

---

If you want, I can make a **diagram showing how roles propagate settings from category → nodes → services**, including DockerHost, which makes it much easier to visualize.

Do you want me to make that diagram?
