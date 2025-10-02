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

Got it 👍 — let me give you a **complete reference of `pdsh` commands, options, and examples** in one place.

---

# **`pdsh` (Parallel Distributed Shell) – Full Command Reference**

`pdsh` lets you run a shell command on multiple nodes in parallel. Very useful in cluster and HPC administration.

---

## **Basic Syntax**

```bash
pdsh [OPTIONS] command
```

---

## **Node Selection Options**

| Option               | Meaning                                         |
| -------------------- | ----------------------------------------------- |
| `-w host1,host2,...` | Run on **explicit list of hosts**               |
| `-w node[01-10]`     | Run on a **range of hosts**                     |
| `-g group`           | Run on a **genders group** (like `computenode`) |
| `-x host1,...`       | **Exclude** listed hosts                        |
| `-A`                 | Run on **all nodes** (even if down)             |
| `-v`                 | Run only on **nodes that are UP**               |

---

## **Execution Options**

| Option      | Meaning                                                     |
| ----------- | ----------------------------------------------------------- |
| `-f n`      | Fanout: **limit number of parallel connections**            |
| `-R <rcmd>` | Choose remote execution method (`ssh`, `exec`, `rsh`, etc.) |
| `-t n`      | Timeout for command execution                               |
| `-N`        | Disable hostname prefix in output                           |
| `-l user`   | Run command as **different user**                           |
| `-p`        | Print **target hosts only** (no execution)                  |
| `-i`        | Expand hostnames to **canonical names**                     |
| `-S`        | Print node name before command output                       |

---

## **Output Formatting**

| Option | Meaning                             |
| ------ | ----------------------------------- |
| `-h`   | Suppress hostname output in results |
| `-L`   | Label output with **node name**     |
| `-q`   | Quiet mode, suppress error messages |
| `-E`   | Redirect stderr separately          |
| `-N`   | Output without hostnames (raw)      |

---

## **Common Examples**

* Run command on specific nodes:

```bash
pdsh -w node01,node02 hostname
```

* Run on a range:

```bash
pdsh -w node[01-05] uptime
```

* Run on genders group:

```bash
pdsh -g computenode df -h
```

* Run on all UP nodes:

```bash
pdsh -v -A uname -r
```

* Exclude a node:

```bash
pdsh -w node[01-05] -x node03 hostname
```

* Limit concurrency (fanout = 5):

```bash
pdsh -f 5 -g computenode date
```

---

## **With `dshbak` for Readable Output**

* Basic coalescing:

```bash
pdsh -g computenode uname -r | dshbak -c
```

* Save outputs in per-host files:

```bash
pdsh -g computenode cat /etc/hosts | dshbak -d /tmp/hosts_output
```

---

## **Running as Another User**

```bash
pdsh -l root -w node[01-05] "systemctl restart sshd"
```

---

## **Mixing Options**

* Run on compute nodes but exclude `node05`, and limit to 3 at a time:

```bash
pdsh -g computenode -x node05 -f 3 uptime
```

* Run across all nodes and show canonical names:

```bash
pdsh -A -i hostname
```

---

## **Quick Cluster Admin Use Cases**

* **Check kernel version on all nodes**:

```bash
pdsh -g computenode uname -r | dshbak -c
```

* **Check uptime on compute nodes**:

```bash
pdsh -g computenode uptime | dshbak -c
```

* **Restart a service everywhere**:

```bash
pdsh -g computenode "systemctl restart slurmd"
```

* **Copy a file to all nodes**:

```bash
pdcp -g computenode /etc/hosts /etc/hosts
```
