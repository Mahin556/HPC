While `sacct` and `sstat` focus on individual jobs, **`sreport` is used for generating summarized reports** about **accounts, users, clusters, and partitions**—mainly for administrators or accounting purposes.

---

### **Basic Usage**

* `sreport` → shows usage reports (default is summarized per account).
* `sreport cluster utilization` → show overall cluster utilization.
* `sreport -h` → show help with all available report types.

---

### **Common Reports**

* **Cluster reports**

  * `sreport cluster utilization` → shows CPU and memory utilization per cluster.
  * `sreport cluster AccountUtilization` → per-account usage across cluster.

* **User reports**

  * `sreport user top` → shows top users by resource usage.
  * `sreport user utilization` → usage of individual users.

* **Account reports**

  * `sreport account utilization` → usage by account/project.
  * `sreport account top` → top accounts consuming resources.

* **Partition reports**

  * `sreport partition utilization` → usage per partition.
  * `sreport partition TopUtilization` → top partitions in terms of resource use.

---

### **Time Filters**

* `Start=` → start date/time
* `End=` → end date/time

Example:

```bash
sreport cluster utilization start=2025-09-01 end=2025-09-29
```

Shows cluster utilization between Sept 1 and Sept 29, 2025.

---

### **Output Control**

* `-n` → no headers
* `-p` → parsable output (pipe-separated)
* `-t` → specify fields/columns (depending on report type)

---

### **Examples**

* Top 10 users by CPU usage:

```bash
sreport user top
```

* Account usage summary for September:

```bash
sreport account utilization start=2025-09-01 end=2025-09-29
```

* Cluster CPU and memory utilization:

```bash
sreport cluster utilization
```

* Partition-wise utilization:

```bash
sreport partition utilization
```

---

### **Notes**

* `sreport` **requires Slurm accounting to be enabled**.
* Unlike `sacct`, it’s **aggregate/historical**, not for live monitoring.
* Useful for **billing, reporting, and capacity planning**.

---

If you want, I can now prepare a **full Slurm command reference table** including:

**`salloc`, `srun`, `sbatch`, `sacct`, `sstat`, `sreport`**

…with **purpose, key options, examples**, so you have **everything in one place** for practice and reference.
