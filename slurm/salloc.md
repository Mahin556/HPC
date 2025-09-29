`salloc` is used to allocate resources (nodes, CPUs, memory, GPUs, etc.) for interactive jobs. It is similar to `sbatch` and `srun`, but it gives you a shell with allocated resources instead of running a batch job.

---

### **Basic Allocation**

* `salloc` → allocate default resources (usually 1 CPU, 1 node, short time).
* `salloc -n 4` → allocate 4 tasks (similar to `--ntasks=4`).
* `salloc -N 2` → allocate 2 nodes.
* `salloc -c 2` → allocate 2 CPUs per task.
* `salloc --mem=8G` → allocate 8 GB memory for the job.
* `salloc --gres=gpu:2` → allocate 2 GPUs.
* `salloc --time=01:00:00` → request 1 hour runtime.
* `salloc --partition=debug` → request allocation on the "debug" partition.
* `salloc --account=myproject` → run job under "myproject" account.
* `salloc --qos=high` → request a specific Quality of Service (QOS).

---

### **Job Identification**

* `salloc --job-name=testjob` → set job name.
* `salloc --workdir=/scratch/mydir` → set working directory.
* `salloc --chdir=/scratch/mydir` → same as above.
* `salloc --comment="testing new script"` → add a comment for job tracking.

---

### **Advanced Node/CPU Binding**

* `salloc --ntasks=8` → request 8 tasks total.
* `salloc --ntasks-per-node=4` → request 4 tasks per node.
* `salloc --nodes=2-4` → request between 2 and 4 nodes.
* `salloc --exclusive` → request nodes exclusively (no sharing).
* `salloc --hint=nomultithread` → disable hyperthreading usage.
* `salloc --distribution=block` → allocate tasks in block distribution.
* `salloc --cpu-bind=cores` → bind tasks to cores.

---

### **Job Duration / Priority**

* `salloc --time=30:00` → 30 minutes allocation.
* `salloc --begin=now+1hour` → start job 1 hour later.
* `salloc --deadline=2025-09-30T12:00:00` → must complete before deadline.
* `salloc --priority=10000` → request specific priority (admin use).

---

### **Constraints & Resources**

* `salloc --constraint=skylake` → only use Skylake CPUs.
* `salloc --gres=gpu:tesla:2` → request 2 Tesla GPUs.
* `salloc --gres-flags=disable-binding` → disable GPU/CPU binding.
* `salloc --mem-per-cpu=4G` → allocate 4 GB per CPU.
* `salloc --licenses=matlab@licsrv:1` → request 1 MATLAB license.
* `salloc --reservation=myreservation` → use a reservation.

---

### **Output & Debugging**

* `salloc --verbose` → verbose output.
* `salloc --test-only` → show what would be allocated, don’t actually allocate.
* `salloc --immediate` → fail if resources are not immediately available.
* `salloc --no-shell` → don’t launch a shell after allocation.
* `salloc --overlap` → allow job overlap with existing allocation.
* `salloc --export=ALL` → export environment variables to job.
* `salloc --export=NONE` → do not export env variables.
* `salloc --export=VAR1=value,VAR2=value` → export specific env vars.
* `salloc --pty bash` → get an interactive shell directly.
* `salloc --kill-command` → kill job if launch command fails.

---

### **Accounting / Fairshare**

* `salloc --account=group1` → charge job to account "group1".
* `salloc --qos=express` → request "express" QoS.

---

### **Miscellaneous**

* `salloc --mail-user=myemail@domain.com` → send job emails.
* `salloc --mail-type=ALL` → send email on BEGIN, END, FAIL.
* `salloc --dependency=afterok:12345` → start only after job 12345 succeeds.
* `salloc --signal=USR1@60` → send SIGUSR1 60 seconds before timeout.
* `salloc --hold` → submit in held state, must release with `scontrol release`.
* `salloc --requeue` → allow job to be requeued.

---

### **Practical Examples**

* `salloc -N 2 -n 8 -t 01:00:00 --mem=16G --pty bash`
  → allocate 2 nodes, 8 tasks, 16 GB memory, 1-hour runtime, and launch interactive shell.
* `salloc --gres=gpu:1 -c 4 --mem=10G --time=2:00:00 --pty bash`
  → allocate 1 GPU, 4 CPUs, 10 GB memory, 2 hours.
* `salloc -N 1 -n 1 --constraint=skylake --time=30:00 --pty python3`
  → allocate a Skylake node for 30 minutes and run Python interactively.