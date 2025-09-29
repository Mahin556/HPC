`sacct` is used to **report job/accounting information** after jobs have run (finished, failed, cancelled, etc.). It queries the Slurm accounting database.

---

### **Basic Usage**

* `sacct` → show jobs run by you (default: today).
* `sacct -j 12345` → show info for job ID `12345`.
* `sacct -u username` → show jobs of a specific user.
* `sacct -A project1` → show jobs charged to account/project1.
* `sacct -p` → output in parsable format (pipe-separated).
* `sacct -X` → suppress job steps (only top-level jobs).

---

### **Time Filtering**

* `sacct -S 2025-09-28` → show jobs starting after Sept 28, 2025.
* `sacct -E 2025-09-29` → show jobs ending before Sept 29, 2025.
* `sacct -S now-1days` → jobs from last 1 day.
* `sacct -S 00:00 -E now` → jobs since midnight until now.

---

### **Job State Filtering**

* `sacct -s RUNNING` → only running jobs.
* `sacct -s FAILED` → only failed jobs.
* `sacct -s COMPLETED` → only completed jobs.
* `sacct -s PENDING,FAILED` → multiple states.

---

### **Output Formatting**

* `sacct -o JobID,JobName,Partition,Account,AllocCPUs,Elapsed,State,ExitCode`
  → specify which fields to display.
* `sacct -e` → show all possible fields (very long list).
* `sacct --format=User,JobID,JobName%30,Elapsed,NNodes,State`
  → custom format, with JobName column width set to 30.
* `sacct -n -o jobid,state` → no headers, only jobid and state.
* `sacct --parsable2` → machine-friendly format with `|` separators.

---

### **Step Information**

* `sacct -j 12345` → shows job + job steps (default).
* `sacct -j 12345 -X` → only shows the parent job (no steps).
* `sacct -j 12345.batch` → only show the batch step of the job.

---

### **Examples**

* `sacct -j 67890 -o JobID,JobName,Partition,AllocCPUs,Elapsed,State,ExitCode`
  → detailed info for job `67890`.
* `sacct -u myuser -S 2025-09-01 -E 2025-09-29`
  → all jobs for `myuser` in September.
* `sacct -A research --state=FAILED`
  → failed jobs under account `research`.
* `sacct -s TIMEOUT -o JobID,User,Elapsed,ReqMem,State`
  → jobs that timed out with memory info.

---

### **Common Fields You’ll Use in `-o`**

(Some cluster admins may configure which are available.)

* `JobID` → Job ID number
* `JobName` → Name of job
* `User` → Username
* `Partition` → Partition used
* `Account` → Account/project charged
* `AllocCPUs` → Number of allocated CPUs
* `AllocNodes` → Number of allocated nodes
* `NNodes` → Nodes used
* `ReqMem` → Requested memory
* `MaxRSS` → Maximum memory used
* `Elapsed` → Time elapsed
* `Submit` → Submission time
* `Start` → Start time
* `End` → End time
* `ExitCode` → Exit code
* `State` → Job state (COMPLETED, FAILED, TIMEOUT, etc.)
* `NodeList` → List of nodes used

---

### **Quick Cheatsheet Examples**

* Jobs completed today (summary):
  `sacct -s COMPLETED -o JobID,JobName,Elapsed,State`
* Jobs that failed in last 2 days:
  `sacct -S now-2days -s FAILED`
* Show memory usage per job:
  `sacct -o JobID,JobName,AllocCPUs,ReqMem,MaxRSS`

