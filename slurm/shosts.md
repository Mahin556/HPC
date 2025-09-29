### shosts

`shosts` = “show hosts.”
It summarizes node state in a table format:

* **HOSTNAME** → node name
* **PARTITION** → partition the node belongs to
* **STATE** → `idle`, `alloc`, `mix`, `drain`, `down`, etc.
* **CPUS** → total CPUs on node
* **MEM** → memory on node
* **JOBS** → jobs running on that node

So it’s like a shortcut to **`sinfo -N -l`** but with better formatting.

---

🔹 **2. Syntax**
```bash
shosts [OPTIONS]
```

---

🔹 **3. Common Commands & Options**

**Show all nodes**
```bash
shosts
```

Equivalent to:
```bash
sinfo -N
```

---

**Show long/detailed info**
```bash
shosts -l
```

Shows CPUs, sockets, cores, memory, state, and jobs.

---

**Show jobs running on each host**
```bash
shosts -j
```

Example output:
```
HOSTNAME   PARTITION   STATE   JOBS
node01     compute     alloc   12345,12346
node02     compute     idle    -
```

---

**Filter by partition**
```bash
shosts -p debug
```

Equivalent to:
```bash
sinfo -N -p debug
```

---

**Filter by state**
```bash
shosts -t idle
shosts -t alloc
shosts -t down
```

---

**Show specific node(s)**
```bash
shosts -n node01
shosts -n node[01-04]
```

---

**Combine options**
```bash
shosts -p compute -t idle -l
```

---

🔹 **4. Node States You’ll See**

* **idle** → Node is free
* **alloc** → Node fully allocated
* **mix** → Node partially allocated
* **drain** → Drained, not available for scheduling
* **down** → Node is down/unavailable
* **resv** → Node reserved (e.g., via `scontrol create reservation`)
* **fail** → Node reported failure

---

🔹 **5. Comparison with Other SLURM Tools**

| Tool                 | Scope              | Use Case                                                              |
| -------------------- | ------------------ | --------------------------------------------------------------------- |
| `shosts`             | Hosts summary      | Quick overview of nodes, CPUs, jobs (compact table)                   |
| `sinfo`              | Partitions & nodes | Scheduler view: partitions, availability, states                      |
| `scontrol show node` | Full node details  | Deep inspection of a single node (CPUs, memory, gres, features, etc.) |
| `squeue -w <node>`   | Jobs on a node     | See which jobs are using a given node                                 |

---

🔹 **6. Examples for Daily Use**

* **Check all nodes:**
```bash
shosts
```

* **See which jobs are running on nodes:**
```bash
shosts -j
```

* **Check idle nodes in partition `compute`:**
```bash
shosts -p compute -t idle
```

* **Detailed info for node group:**
```bash
shosts -n node[01-10] -l
```

* **List drained nodes only:**
```bash
shosts -t drain
```

---

🔹 **7. Advanced Admin Usage**

Since `shosts` is read-only, admins typically combine it with `scontrol`:

* Find drained nodes:
```bash
shosts -t drain
```

Then bring one back:
```bash
scontrol update NodeName=node05 State=RESUME
```

* Find down nodes:
```bash
shosts -t down
```

Then mark for maintenance:
```bash
scontrol update NodeName=node10 State=DRAIN Reason="Disk failure"
```
```
shosts | grep completing
shosts | grep down
shosts | grep draining
```