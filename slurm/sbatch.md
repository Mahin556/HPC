If `salloc` is for interactive jobs, and `srun` runs tasks, then **`sbatch` is for submitting batch scripts** to the scheduler (non-interactive jobs).

---

### **Basic Usage**

* `sbatch script.sh` → submit a job script.
* `sbatch --wait script.sh` → wait until job finishes, then return.
* `sbatch --job-name=testjob script.sh` → submit with job name.

---

### **Resource Requests**

(These can be specified on the command line **or inside the script** using `#SBATCH` directives.)

* `sbatch -n 4 script.sh` → 4 tasks.
* `sbatch -N 2 script.sh` → 2 nodes.
* `sbatch -c 2 script.sh` → 2 CPUs per task.
* `sbatch --mem=8G script.sh` → 8 GB memory per node.
* `sbatch --mem-per-cpu=2G script.sh` → 2 GB per CPU.
* `sbatch --gres=gpu:2 script.sh` → 2 GPUs.
* `sbatch --time=01:00:00 script.sh` → 1 hour runtime.

---

### **Job Identification**

* `sbatch -J myjob script.sh` → set job name.
* `sbatch -o out.%j script.sh` → stdout goes to `out.<jobid>`.
* `sbatch -e err.%j script.sh` → stderr goes to `err.<jobid>`.
* `sbatch -D /scratch/mydir script.sh` → set working directory.

---

### **Partition / Account / QOS**

* `sbatch -p debug script.sh` → run in "debug" partition.
* `sbatch -A project1 script.sh` → run under account/project1.
* `sbatch --qos=high script.sh` → request "high" QoS.

---

### **Job Dependencies**

* `sbatch --dependency=afterok:12345 script.sh` → start after job `12345` completes successfully.
* `sbatch --dependency=afterany:12345 script.sh` → start after job finishes (success/fail).
* `sbatch --dependency=singleton script.sh` → only one job with same name runs at a time.

---

### **Output & Email**

* `sbatch --mail-user=myemail@domain.com --mail-type=ALL script.sh` → get emails on job state changes.
* `sbatch --mail-type=BEGIN,END,FAIL script.sh` → specific notifications.
* `sbatch --open-mode=append script.sh` → append output instead of overwrite.

---

### **Job Control**

* `sbatch --hold script.sh` → submit job in "held" state (release with `scontrol release`).
* `sbatch --requeue script.sh` → allow requeue if node fails.
* `sbatch --signal=USR1@60 script.sh` → send signal before timeout.
* `sbatch --kill-on-invalid-dep=yes script.sh` → kill job if dependency invalid.

---

### **Constraints**

* `sbatch --constraint=skylake script.sh` → run only on Skylake CPUs.
* `sbatch --reservation=myres script.sh` → use reserved resources.
* `sbatch --licenses=matlab:2 script.sh` → request 2 MATLAB licenses.

---

### **Inside a Job Script**

Job scripts usually start with `#!/bin/bash` and then **Slurm directives**:

```bash
#!/bin/bash
#SBATCH --job-name=myjob
#SBATCH --output=out.%j
#SBATCH --error=err.%j
#SBATCH --time=01:00:00
#SBATCH --nodes=2
#SBATCH --ntasks=8
#SBATCH --cpus-per-task=2
#SBATCH --mem=16G
#SBATCH --partition=debug
#SBATCH --mail-type=ALL
#SBATCH --mail-user=myemail@domain.com

# Run commands
module load python
srun python myscript.py
```

---

### **Practical Examples**

* Submit 4-task job with 2 CPUs each, 1 hour runtime:

  ```bash
  sbatch -n 4 -c 2 --time=01:00:00 myjob.sh
  ```
* Submit GPU job:

  ```bash
  sbatch --gres=gpu:1 --time=2:00:00 gpu_job.sh
  ```
* Submit with dependencies:

  ```bash
  sbatch --dependency=afterok:12345 analysis.sh
  ```

---

### **Most Useful Options Cheatsheet**

* **Resources** → `-n`, `-N`, `-c`, `--mem`, `--gres`, `--time`
* **Job identity** → `-J`, `-o`, `-e`, `-D`
* **Control** → `--hold`, `--requeue`, `--dependency`
* **Partition/account** → `-p`, `-A`, `--qos`
* **Notifications** → `--mail-user`, `--mail-type`

