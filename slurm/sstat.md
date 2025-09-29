While `sacct` reports **finished jobs** from the accounting database, **`sstat` reports **real-time status of running jobs** or job steps. It’s mainly for monitoring **resource usage** while jobs are active.

---

### **Basic Usage**

* `sstat -j <jobid>` → show status of a running job.
* `sstat -j <jobid>.batch` → show batch step only.
* `sstat -j <jobid>.<stepid>` → show specific step.

---

### **Common Options**

* `-p` → output in **parsable (pipe-separated)** format.
* `-n` → suppress headers.
* `-o` → specify **fields/columns** to display.
* `-a` → show all available fields.
* `-S <starttime>` → show data since given time.
* `-E <endtime>` → show data until given time.
* `--allsteps` → show all steps of the job.
* `-l` → long format (detailed information).

---

### **Common Fields**

Use with `-o` option, e.g., `sstat -j 12345 -o JobID,MaxRSS,Elapsed`

Some frequently used fields:

* `JobID` → job ID or job step ID
* `JobName` → name of the job
* `MaxRSS` → max resident set size (memory usage)
* `MaxVMSize` → max virtual memory used
* `AveCPU` → average CPU time used
* `AllocCPUS` → allocated CPUs
* `NTasks` → number of tasks
* `State` → current state of the job step
* `Elapsed` → elapsed time
* `NodeList` → nodes where job runs
* `ReqMem` → requested memory

---

### **Examples**

* Show memory usage and elapsed time for a running job:

  ```bash
  sstat -j 12345 -o JobID,JobName,MaxRSS,AveCPU,Elapsed
  ```
* Show batch step only:

  ```bash
  sstat -j 12345.batch -o JobID,State,MaxRSS
  ```
* Show all steps of a job:

  ```bash
  sstat -j 12345 --allsteps -o JobID,JobName,MaxRSS,AveCPU,Elapsed
  ```
* Real-time CPU/memory info:

  ```bash
  sstat -j 12345 -o JobID,AllocCPUS,AveCPU,MaxRSS
  ```

---

### **Notes**

* `sstat` **only works for running jobs or recently finished jobs** (depending on accounting configuration).
* For historical data, use `sacct`.
* Can combine with `watch` for continuous monitoring:

  ```bash
  watch sstat -j 12345 -o JobID,MaxRSS,AveCPU,Elapsed
  ```
