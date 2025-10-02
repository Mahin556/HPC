## **Chapter Overview: Day-to-Day Administration in Bright Cluster Manager**

Bright Cluster Manager (BCM) provides tools and commands for **efficient cluster administration**. Administrators can manage nodes, configurations, images, hardware, and monitoring data systematically.
---

## **17.1 Parallel Shells: `pdsh` and `pexec`**

### **Purpose**

`pdsh` and `pexec` allow administrators to **run commands on multiple nodes simultaneously**, saving time when managing large clusters.

* **pdsh (Parallel Distributed Shell)**

  * Runs from the **OS shell** (bash by default).
  * Ideal for one-time commands or scripts executed from the head node.

* **pexec (Parallel Execute)**

  * Runs from **cmsh** (CMDaemon shell).
  * Executes commands on nodes directly from BCM’s management interface.

> **Warning:** Be careful with **power-related commands**. Running power cycles in parallel across many nodes can cause **power surges**. Using `pexec` from `cmsh` is safer, as it includes **delays between node resets**.

---

### **17.1.1 `pdsh` in the OS Shell**

#### **Required Packages**

To use `pdsh` fully, the following packages must be installed on the head node:

* `pdsh`
* `genders`
* `pdsh-mod-cmupdown`
* `pdsh-mod-genders`
* `pdsh-rcmd-exec`
* `pdsh-ssh`

#### **What is `genders`?**

* Generates **node groups** in `/etc/genders`.
* Default categories:

  * `all` → all nodes
  * `category=default` → default nodes
  * `computenode` → regular compute nodes
  * `headnode` → head nodes

> `pdsh-mod-cmupdown` tracks **node state**, so admins don’t need to manually edit `/etc/genders`.

---

#### **pdsh Options**

```bash
pdsh [options] command
```

Some key options:

| Option           | Description                                      |
| ---------------- | ------------------------------------------------ |
| `-A`             | Target **all nodes**, even if down               |
| `-v`             | Target **only nodes that are UP**                |
| `-g <group>`     | Target nodes using a **genders group**           |
| `-w host1,host2` | Explicit **node list**                           |
| `-x host1`       | Exclude specific nodes                           |
| `-f n`           | Fanout – number of concurrent connections        |
| `-R <module>`    | Use a specific **rcmd module** (`ssh` or `exec`) |
| `-i`             | Request canonical hostnames                      |

---

#### **Example Commands**

Assume a cluster with **node001 UP** and **node002 DOWN**.

* Run on **all nodes**:

```bash
pdsh -A hostname
# Output shows ssh error for node002
```

* Run only on **UP nodes**:

```bash
pdsh -A -v hostname
# Output: node001 and head node only
```

* Run on **genders group `computenode`**:

```bash
pdsh -v -g computenode hostname
# Only up compute nodes executed
```

* Run on **explicit node list**:

```bash
pdsh -w node00[1-2] hostname
```

* Exclude specific nodes:

```bash
pdsh -x node002 -w node00[1-2] hostname
```

---

### **dshbak Utility**

* Reformats `pdsh` output for **human readability**.
* Key options:

| Option   | Description                                   |
| -------- | --------------------------------------------- |
| `-c`     | Coalesce identical output from multiple hosts |
| `-d DIR` | Output to separate files per host             |

**Example:**

```bash
pdsh -A ls /etc/services /etc/yp.conf | dshbak -c
```

---

### **17.1.2 `pexec` in cmsh**

* Run commands from **device mode** in `cmsh`:

```bash
[bright91->device]% pexec -n node001,node002 "cd; ls"
[node001]: ...
[node002]: ...
```

* In **Bright View GUI**, `pexec` is wrapped into the **Cluster!Run** interface.

* Use `-j|--join` to **merge identical outputs**, making it easier to compare nodes:

```bash
pexec -j -c default "cat /etc/resolv.conf"
# Combines identical lines for multiple nodes
```

---

### **17.1.5 Other Parallel Commands in CMDaemon**

| Command | Purpose                                                       |
| ------- | ------------------------------------------------------------- |
| `pkill` | Kill processes **in parallel** across nodes                   |
| `plist` | List **currently running parallel commands** with tracker IDs |
| `pping` | **Ping multiple nodes** in parallel                           |
| `pwait` | Wait for **parallel commands to complete**                    |

> Use `help <command>` in cmsh device mode to see detailed usage.

---

### **Tips and Best Practices**

* Prefer **pexec over pdsh** for commands with **power or critical operations**, to avoid node-wide power surges.
* Use **genders groups** to target nodes logically (`computenode`, `headnode`).
* For **large clusters**, use `dshbak` or `pexec -j` to **merge outputs** and simplify reading.
* For scripts, combine `pdsh` and `pexec` with **node state checks** (`-v`) to avoid errors.

---

### **Additional Enhancements**

* **Automating health checks**:

```bash
pdsh -v -g computenode "uptime; df -h; free -m"
```

* **Cluster-wide config verification**:

```bash
pexec -j -c default "cat /etc/hosts | grep cluster"
```

* **Scheduled parallel tasks**: Combine with `cron` on head node for **periodic checks or updates**.
* **Logging and auditing**: Pipe outputs to centralized logs for compliance or troubleshooting:

```bash
pdsh -v -g computenode "systemctl status kubelet" | dshbak -c > /var/log/cluster_kubelet_status.log
```
