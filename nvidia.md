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
