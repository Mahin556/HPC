### sinfo
🔹 **1. Basic Syntax**
```bash
sinfo [OPTIONS]
```

Default (no options):
```bash
sinfo
```
Shows partitions, node states, and counts.

---

🔹 **2. Common Output Fields**
Default `sinfo` shows:
| Field         | Meaning                                                    |
| ------------- | ---------------------------------------------------------- |
| **PARTITION** | Partition (queue) name                                     |
| **AVAIL**     | Availability (`up`, `down`)                                |
| **TIMELIMIT** | Max wall time allowed for jobs in this partition           |
| **NODES**     | Count of nodes in this state                               |
| **STATE**     | Node state (`idle`, `alloc`, `mix`, `drain`, `down`, etc.) |
| **NODELIST**  | List of nodes in that state                                |

Example:
```
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
compute     up     1-00:00:00     5  alloc node[01-05]
compute     up     1-00:00:00     3   idle node[06-08]
debug       up     00:30:00       2   idle node[09-10]
```

---
🔹 **3. Useful Options**
Show nodes instead of partitions
```bash
sinfo -N
```
(add `-l` for long output)

---
Show detailed info
```bash
sinfo -l
```
Includes CPUs, memory, sockets, cores, threads, features.

---
Show both nodes & partitions (long form)
```bash
sinfo -Nl
```

---
Show only specific partition
```bash
sinfo -p compute
```

---
Show only specific nodes
```bash
sinfo -n node01
sinfo -n node[01-04]
```

---
Show partitions in summary
```bash
sinfo -s
```
Example output:
```
PARTITION AVAIL TIMELIMIT NODES(A/I/O/T)
compute   up    1-00:00:00  5/3/0/8
debug     up    00:30:00    0/2/0/2
```
(A=Allocated, I=Idle, O=Other, T=Total)

---
Show by state
```bash
sinfo -t idle
sinfo -t alloc
sinfo -t drain,down
```

---
Continuous refresh (like `top`)
```bash
watch -n 2 sinfo
```
---
🔹 **4. Formatting Output (`-o`)**
Custom output format:
```bash
sinfo -o "%P %D %T %N"
```

Common format specifiers:
| Specifier | Meaning                              |
| --------- | ------------------------------------ |
| `%P`      | Partition name                       |
| `%a`      | Availability                         |
| `%l`      | Time limit                           |
| `%D`      | # of nodes                           |
| `%t`      | State (short)                        |
| `%T`      | State (long)                         |
| `%N`      | Node list                            |
| `%c`      | CPUs per node                        |
| `%m`      | Real memory per node                 |
| `%f`      | Features                             |
| `%G`      | Generic resources (GRES, e.g., GPUs) |

---

🔹 **5. Node States**
Nodes can be in states like:
* **idle** → free
* **alloc** → fully allocated
* **mix** → partially allocated
* **drain** → being drained (no new jobs)
* **down** → unavailable
* **fail** → marked failed
* **maint** → under maintenance
* **resv** → reserved

---

🔹 **6. Practical Examples**
* **Quick overview of partitions:**
```bash
sinfo -s
```

* **Show nodes and their states:**
```bash
sinfo -N
```

* **Show idle nodes only:**
```bash
sinfo -t idle -N
```

* **Custom node listing with memory and CPUs:**
```bash
sinfo -N -o "%N %c %m %T %P"
```

* **Check GPU partitions:**
```bash
sinfo -p gpu -o "%N %G %T"
```

---
🔹 **7. Comparison: `sinfo` vs `squeue` vs `scontrol`**
| Command                | Shows                                         |
| ---------------------- | --------------------------------------------- |
| **sinfo**              | Cluster structure (partitions, nodes, states) |
| **squeue**             | Jobs (pending, running, etc.)                 |
| **scontrol show node** | Detailed info about a single node             |


