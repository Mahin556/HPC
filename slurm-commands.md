### scontrol

- General
  ```
  scontrol show jobs
  scontrol show nodes
  scontrol show partitions
  scontrol show config #show slurm config
  ```

- View job information
  ```
  scontrol show job <job_id>
  ```
  Displays full info: job name, state, nodes, CPUs, memory, time limits, dependencies, etc.
  Options:
  -d → Detailed output
  -o → Output in a single line

- Modify a job
  ```
  scontrol update JobId=<job_id> [Field=Value ...]
  ```
  - Change priority:
    ```
    scontrol update JobId=12345 Priority=1000
    ```
  - Change time limit:
    ```
    scontrol update JobId=12345 TimeLimit=02:00:00
    ```
  
- Hold / Release jobs
  - Hold a job (prevents it from running):
    ```
    scontrol hold <job_id>
    ```
  - Release a held job:
    ```
    scontrol release <job_id>
    ```

- Requeue / Cancel jobs
  - Requeue a job (put back in the queue):
    ```
    scontrol requeue <job_id>
    ```
  - Requeue failed jobs:
    ```
    scontrol requeue <job_id> Force
    ```
  - Cancel a job:
    ```
    scontrol cancel <job_id>
    ```

- Suspend / Resume jobs
  - Suspend a running job:
    ```
    scontrol suspend <job_id>
    ```
  - Resume a suspended job:
    ```
    scontrol resume <job_id>
    ```

    
- View node information
  ```
  scontrol show node <node_name>
  ```
  Displays state, CPUs, memory, partitions, jobs running, etc.
  Options:
  -d → Detailed
  -o → One-line output

- Update node state
  ```
  scontrol update NodeName=<node_name> State=<state> [Reason="text"]
  scontrol update NodeName=node01 State=DOWN Reason="hardware failure"
  scontrol update NodeName=node01 State=RESUME
  scontrol update NodeName=<node> State=DRAIN Reason="maintenance"
  ```
  Common states:
    DOWN → Node is unavailable
    DRAIN → Node will finish current jobs, no new jobs
    RESUME → Bring node back online
    FAIL → Mark node as failed

- View partition information
  ```
  scontrol show partition <partition_name>
  ```

- Update partition
  ```
  scontrol update PartitionName=<partition> [Field=Value ...]
  ```
  - Change max time:
    ```
    scontrol update PartitionName=debug MaxTime=01:00:00
    ```
  - Enable/disable partition:
    ```
    scontrol update PartitionName=debug State=UP
    scontrol update PartitionName=debug State=DOWN
    ```

- Reservation
  - View all active reservations:
    ```
    scontrol show reservation
    ```
  - Show a specific reservation:
    ```
    scontrol show reservation <reservation_name>
    ```
  - Show reservations by user:
    ```
    scontrol show reservation | grep UserId=<username>
    ```
  - Create Reservations
    ```
    scontrol create reservation <parameters>
    ```
    Common parameters:
    - ReservationName=<name> → Name of reservation (default: Resv_JobID_xxx)
    - StartTime=<time> → When reservation begins (now, YYYY-MM-DD[Thh:mm:ss])
    - Duration=<minutes | D-HH:MM:SS> → Length of reservation
    - EndTime=<time> → Alternative to Duration
    - Nodes=<node_list> → Nodes included (node[01-04], ALL)
    - CoreCnt=<count> → Number of cores (instead of full nodes)
    - Features=<features> → Restrict to nodes with specific features
    - Users=<user_list> → Allowed users (comma separated)
    - Accounts=<account_list> → Allowed accounts
    - Groups=<group_list> → Allowed Unix groups
    - Licenses=<license_list> → Reserve licenses
    - Flags=<options> → Special behavior flags
      
  - Example 1: Reserve 4 nodes for user alice
    ```
    scontrol create reservation ReservationName=alice_res StartTime=now Duration=02:00:00 Nodes=node[01-04] Users=alice
    ```
  - Example 2: Reserve a partition for a workshop(users)
    ```
    scontrol create reservation ReservationName=workshop StartTime=2025-09-28T10:00:00 Duration=4:00:00 PartitionName=training Users=user1,user2,user3
    ```
  - Example 3: Reserve cores instead of nodes
    ```
    scontrol create reservation ReservationName=test_res Duration=1:00:00 StartTime=now CoreCnt=32 Users=bob
    ```

  - Update Reservations
    - Modify an existing reservation:
      ```
      scontrol update reservation ReservationName=<name> <parameters>
      ```
    - Extend the duration:
      ```
      scontrol update reservation ReservationName=alice_res Duration=3:00:00
      ```
    - Add another user:
      ```
      scontrol update reservation ReservationName=alice_res Users=alice,bob
      ```
    - Add more nodes:
      ```
      scontrol update reservation ReservationName=alice_res Nodes=node[01-06]
      ```
  - Delete Reservations
    - Cancel a reservation:
      ```
      scontrol delete reservation ReservationName=<name>
      scontrol delete reservation ReservationName=workshop
      ```

  - Flags for Reservations
    - Reservations support special flags via:
      ```
      scontrol create reservation ... Flags=<flag>
      ```
      Common flags:
        MAINT → Maintenance reservation
        DAILY → Recurring daily reservation
        WEEKLY → Recurring weekly reservation
        STATIC_ALLOC → Prevent jobs from oversubscribing reserved CPUs
        IGNORE_JOBS → Create reservation even if jobs are already running
        ANY_NODES → Allow allocation of any available nodes (not strict)
        TIME_FLOAT → Allow flexible start within the reservation window

  - Jobs & Reservations
    - When submitting a job, you can bind it to a reservation:
      ```
      sbatch --reservation=<ReservationName> job.sh
      sbatch --reservation=alice_res -N2 -t 01:00:00 job.sh
      srun --reservation=workshop -n8 --time=2:00:00 --pty bash
      ```


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


