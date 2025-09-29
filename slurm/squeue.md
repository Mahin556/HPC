### squeue
🔹 **1. Basic Syntax**
```bash
squeue [OPTIONS]
```

Without options:
```bash
squeue
```
This shows a default table of all jobs in the system.

---

# 🔹 **2. Common Columns in Output**

| Field                | Meaning                                                                                                          |
| -------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **JOBID**            | Unique ID of the job                                                                                             |
| **PARTITION**        | Partition/queue job is in                                                                                        |
| **NAME**             | Job name                                                                                                         |
| **USER**             | User who submitted job                                                                                           |
| **ST**               | Job state (R=Running, PD=Pending, CG=Completing, F=Failed, CA=Cancelled, etc.)                                   |
| **TIME**             | Runtime used (for running jobs)                                                                                  |
| **NODES**            | Number of nodes allocated                                                                                        |
| **NODELIST(REASON)** | For running jobs: node list. For pending jobs: reason it’s waiting (e.g., `Resources`, `Priority`, `Dependency`) |

---
🔹 **3. Filtering Jobs**
Show jobs for your user:
```bash
squeue -u $USER
```

Show jobs for a specific user:
```bash
squeue -u alice
```

Show jobs for a specific partition:
```bash
squeue -p compute
```

Show jobs for a specific job ID:
```bash
squeue -j 12345
```

Show jobs running on a specific node:
```bash
squeue -w node01
```

---

🔹 **4. Formatting Output**
Wide output
```bash
squeue -l
```
(longer, detailed format)

One-line (easy parsing):
```bash
squeue -o "%.18i %.9P %.8j %.8u %.2t %.10M %.6D %R"
```

* `-o` = custom format.
Common format specifiers:
| Specifier | Meaning             |
| --------- | ------------------- |
| `%i`      | Job ID              |
| `%j`      | Job name            |
| `%u`      | User                |
| `%t`      | Job state           |
| `%M`      | Elapsed time        |
| `%D`      | Number of nodes     |
| `%C`      | CPUs                |
| `%m`      | Memory              |
| `%P`      | Partition           |
| `%r`      | Reason (if pending) |
| `%R`      | Node list / reason  |

---

🔹 **5. Filtering by State**
```bash
squeue -t RUNNING
squeue -t PENDING
squeue -t COMPLETED,FAILED
```

Job states include:
* `PD` → Pending
* `R` → Running
* `CG` → Completing
* `F` → Failed
* `CA` → Cancelled
* `CD` → Completed

---

🔹 **6. Sorting Output**
Sort by start time:
```bash
squeue --sort=S
```

Sort by job ID:
```bash
squeue --sort=i
```

Sort by priority:
```bash
squeue --sort=p
```

You can chain sort keys, e.g.:
```bash
squeue --sort=P,i
```
---

🔹 **7. Admin / Debug Options**
See jobs for all users:
```bash
squeue -a
```

Show job steps too:
```bash
squeue -s
```

 Continuously refresh (like `top`):
```bash
watch -n 2 squeue -u $USER
```

Limit number of jobs displayed:
```bash
squeue -n 20
```
---

🔹 **8. Practical Examples**

* **Show all jobs with full details:**
```bash
squeue -l
```

* **Show pending jobs only, sorted by priority:**
```bash
squeue -t PD --sort=-p
```

* **Custom output with JobID, User, State, Nodes:**
```bash
squeue -o "%.8i %.8u %.2t %.4D %.10R"
```

* **Check jobs on node group:**
```bash
squeue -w node[01-04]
```

---

🔹 **9. Combining with Other Tools**
* Jobs with reservations:
```bash
squeue --reservation=myresv
```

* Jobs in JSON (for scripting):
```bash
squeue --json
```

* Jobs in CSV:
```bash
squeue --Format=JobID,User,State --noheader -t RUNNING
```