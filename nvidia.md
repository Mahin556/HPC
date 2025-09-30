### 🔹 1. `nvidia-smi` – Core Command

`nvidia-smi` is the most important NVIDIA tool to monitor and manage GPUs.

* **Basic Monitoring**

  * `nvidia-smi` → Show GPU summary (processes, usage, memory, driver version, etc.)
  * `nvidia-smi -L` → List GPUs with UUIDs
  * `nvidia-smi -q` → Detailed GPU information (full query)
  * `nvidia-smi -q -d MEMORY` → Show only memory-related details
  * `nvidia-smi -q -d POWER` → Show only power usage details
  * `nvidia-smi dmon` → Live monitoring (similar to `top` for GPUs)
  * `nvidia-smi pmon -i 0` → Live per-process GPU usage

* **Monitoring Options**

  * `nvidia-smi -i <id>` → Select a specific GPU
  * `nvidia-smi --query-gpu=utilization.gpu,utilization.memory --format=csv`
    → Custom queries (usage in CSV format)
  * `nvidia-smi --query-compute-apps=pid,process_name,used_memory --format=csv`
    → See which processes use how much GPU memory
  * `nvidia-smi -l 2` → Refresh every 2 seconds

* **Process Management**

  * `nvidia-smi pmon` → Monitor processes using GPU
  * `nvidia-smi --gpu-reset -i <id>` → Reset a GPU (if no processes running)
  * `nvidia-smi --kill-pid=<pid>` → Kill a process using GPU

* **Power & Performance**

  * `nvidia-smi --persistence-mode=1` → Enable persistence mode (keep driver loaded)
  * `nvidia-smi -pm 1` → Same as above
  * `nvidia-smi --auto-boost-default=0` → Disable auto boost
  * `nvidia-smi -pl <watts>` → Set power limit
  * `nvidia-smi -ac <memClock,graphicsClock>` → Set application clocks
  * `nvidia-smi -rac` → Reset application clocks

* **Driver / Version Info**

  * `nvidia-smi --query-gpu=driver_version,vbios_version --format=csv`
  * `nvidia-smi -q -d SUPPORTED_CLOCKS` → See supported clock speeds

---

### 🔹 2. NVIDIA CUDA Tools (if CUDA toolkit installed)

* `nvcc --version` → Check CUDA compiler version
* `deviceQuery` (in CUDA samples) → Show GPU compute capability & features
* `bandwidthTest` (in CUDA samples) → Measure memory bandwidth

---

### 🔹 3. NVIDIA NVML (C Library, for developers)

* `nvml` is not a direct command but a library that powers `nvidia-smi`.
  Developers can use it in Python/C/C++ to query GPU metrics.

---

### 🔹 4. NVIDIA Debugging & Profiling Tools

* `cuda-gdb` → Debug CUDA applications
* `cuda-memcheck` → Detect memory errors in CUDA programs
* `nsys` (NVIDIA Nsight Systems) → System-wide performance profiling
* `nvprof` (deprecated, replaced by Nsight) → GPU profiling

---

### 🔹 5. NVIDIA Container Toolkit (for Docker/K8s)

* `nvidia-container-cli info` → Show GPU info inside containers
* `nvidia-container-runtime --version` → Check runtime version
* `nvidia-docker run --rm nvidia/cuda:12.2.0-base nvidia-smi` → Run container with GPU support

---

### 🔹 6. Other Useful NVIDIA Commands

* `nvidia-settings` → GUI/CLI tool to configure settings (mostly on desktop systems with X server)

  * Example: `nvidia-settings -q GPUCoreTemp` → Query GPU temperature
* `nvidia-bug-report.sh` → Generate diagnostic logs for NVIDIA support
* `nvidia-persistenced` → Daemon to maintain persistence mode

---

### 🔹 Quick Cheat Examples

```bash
# Monitor GPU every 1s
nvidia-smi -l 1

# Show GPU utilization in CSV
nvidia-smi --query-gpu=index,name,temperature.gpu,utilization.gpu,memory.used,memory.total --format=csv

# Set GPU 0 power limit to 200W
nvidia-smi -i 0 -pl 200

# Kill a GPU process by PID
nvidia-smi --kill-pid=12345
```

---

👉 This covers **all important NVIDIA GPU-related commands**:

* `nvidia-smi` (monitoring, managing, power control)
* CUDA commands (`nvcc`, `deviceQuery`, profiling)
* NVIDIA Container Toolkit commands
* Debugging/profiling tools (`cuda-gdb`, `nsys`, `cuda-memcheck`)
* Utility (`nvidia-settings`, `nvidia-bug-report.sh`)

---

### 🔹 1. `nvidia-smi` Logging Options

* `nvidia-smi -l 1`
  Continuously log GPU stats every 1 second (to screen).
* `nvidia-smi -lms 500`
  Log every 500 milliseconds.
* `nvidia-smi --format=csv --query-gpu=timestamp,utilization.gpu,memory.used,memory.total`
  Output in CSV format (good for logging into a file).
* `nvidia-smi --query-compute-apps=pid,process_name,used_memory --format=csv`
  Log which processes use GPU memory.

👉 Redirect output to file:

```bash
nvidia-smi -l 1 --query-gpu=timestamp,utilization.gpu,temperature.gpu,memory.used,memory.total --format=csv > gpu_log.csv
```

---

### 🔹 2. NVIDIA Error & Driver Logs

* `/var/log/nvidia-installer.log` → Logs from NVIDIA driver installation
* `/var/log/nvidia-uninstall.log` → Logs from uninstallation
* `/var/log/nvidia-driver.log` → Driver service logs (may vary by distro)
* `/var/log/Xorg.0.log` → Contains NVIDIA driver initialization info (desktop systems)
* `dmesg | grep -i nvidia` → Kernel ring buffer logs (driver events, GPU resets, errors)
* `journalctl -u nvidia-persistenced` → Logs from persistence daemon
* `journalctl -k | grep -i nvidia` → Kernel-related NVIDIA messages

---

### 🔹 3. NVIDIA Bug Report Tool

* `nvidia-bug-report.sh`
  Generates a full diagnostic tarball with all NVIDIA-related logs.
  (Very useful for troubleshooting with NVIDIA support.)

---

### 🔹 4. Logging GPU Application Performance

* `nvidia-smi dmon`
  Logs real-time GPU utilization, memory, power, clocks.
  Example (log to file every 1s):

  ```bash
  nvidia-smi dmon -s u -d 1 > gpu_usage.log
  ```

  (`-s u` = utilization, `-d 1` = delay 1s)
* `nvidia-smi pmon`
  Per-process monitoring log.

---

### 🔹 5. NVIDIA Nsight / Profiling Logs

* `nsys profile -o output_report ./my_app`
  Creates detailed profiling logs.
* `cuda-memcheck ./app`
  Logs CUDA memory errors.
* `cuda-gdb ./app`
  Debugging logs.

---

✅ **In summary – NVIDIA log-related commands:**

* **Monitoring logs** → `nvidia-smi -l`, `nvidia-smi dmon`, `nvidia-smi pmon`
* **Driver/system logs** → `/var/log/nvidia*.log`, `dmesg | grep nvidia`, `journalctl`
* **Diagnostics** → `nvidia-bug-report.sh`
* **Profiling logs** → `nsys`, `cuda-memcheck`
