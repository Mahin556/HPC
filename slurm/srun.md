`srun` is one of the **most important Slurm commands** – it **runs parallel jobs** (MPI, OpenMP, or simple tasks) and can also be used interactively.

---

### **Basic Usage**

* `srun hostname` → run `hostname` on one CPU.
* `srun -n 4 hostname` → run `hostname` on 4 tasks (parallel).
* `srun -N 2 -n 8 hostname` → run 8 tasks across 2 nodes.
* `srun --pty bash` → start an interactive shell on allocated node(s).
* `srun --jobid=12345 hostname` → run inside an existing job allocation.
* `srun --nodelist=node01,node02 -n 4 hostname` -> run task on specific nodes.
* `srun --exclude=node05,node06 -n 8 hostname` -> Exclude certain nodes.
---

### **Job Resource Requests**

* `srun -n 4` → number of tasks (`--ntasks=4`).
* `srun -N 2` → number of nodes.
* `srun -c 2` → CPUs per task.
* `srun --mem=8G` → total memory per node.
* `srun --mem-per-cpu=2G` → memory per CPU.
* `srun --gres=gpu:2` → 2 GPUs.
* `srun --time=01:00:00` → job time limit.

---

### **Task Placement & Binding**

* `srun --ntasks-per-node=4` → 4 tasks per node.
* `srun --ntasks-per-socket=2` → 2 tasks per socket.
* `srun --cpus-per-task=8` → each task gets 8 CPUs.
* `srun --hint=nomultithread` → avoid hyperthreading.
* `srun --cpu-bind=cores` → bind tasks to cores.
* `srun --distribution=block` → block distribution across nodes.

---

### **Partition / Account / QOS**

* `srun -p debug` → run on partition "debug".
* `srun -A project1` → use account "project1".
* `srun --qos=high` → request "high" QoS.

---

### **Job Management**

* `srun --exclusive` → don’t share nodes with other jobs.
* `srun --overlap` → overlap job steps in same allocation.
* `srun --dependency=afterok:12345` → run only if job `12345` finishes successfully.
* `srun --kill-on-bad-exit=1` → kill all tasks if one fails.
* `srun --signal=USR1@60` → send SIGUSR1 60 seconds before timeout.
* `srun --wait=0` → don’t wait for all tasks to finish before returning.

---

### **Output Control**

* `srun -o out.%j` → write stdout to `out.<jobid>`.
* `srun -e err.%j` → write stderr to `err.<jobid>`.
* `srun -i input.txt` → read stdin from file.
* `srun -l` → label output with task numbers.
* `srun --unbuffered` → unbuffered output (real-time logs).
* `srun --open-mode=append` → append to output files.

---

### **Interactive Jobs**

* `srun --pty bash` → get an interactive shell on allocated node.
* `srun --x11=first` → X11 forwarding for GUIs.
* `srun --mpi=pmix` → launch MPI job with PMIx.

---

### **Debugging & Testing**

* `srun -n 4 hostname` → check distribution of 4 tasks.
* `srun -N 1 -n 2 --cpu-bind=cores sleep 10` → test CPU binding.
* `srun --test-only` → check request without running job.

---

### **Examples**

* Run 16 tasks on 2 nodes (8 per node):

  ```bash
  srun -N 2 -n 16 --ntasks-per-node=8 hostname
  ```
* Run MPI program with 32 tasks and 2 CPUs per task:

  ```bash
  srun -n 32 -c 2 ./mpi_program
  ```
* Interactive GPU job with 1 GPU and 8 CPUs:

  ```bash
  srun --gres=gpu:1 -c 8 --pty bash
  ```
* Run job inside an existing allocation:

  ```bash
  salloc -N 1 -n 4
  srun hostname
  ```

---

### **Most Useful Options Cheat-Sheet**

* **Resources** → `-n`, `-N`, `-c`, `--mem`, `--gres`
* **Placement** → `--ntasks-per-node`, `--distribution`, `--cpu-bind`
* **Job control** → `--exclusive`, `--kill-on-bad-exit`, `--dependency`
* **Output** → `-o`, `-e`, `-l`, `--unbuffered`
* **Interactive** → `--pty bash`, `--x11`
* **MPI** → `--mpi=pmi2 | pmix | none`
