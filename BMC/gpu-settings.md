# 🔹 1. Understanding GPUs in a Cluster

### 1.1 What is a GPU?

* **Graphics Processing Unit (GPU)**: Specialized processor optimized for parallel computation.
* Originally for graphics; used for **general-purpose computing** (GPGPU).
* Features **hundreds of cores** for simultaneous tasks.

---

### 1.2 Internal vs External GPUs

* **Internal GPU**: Installed directly on a node.
* **External GPU**: Housed in a **GPU unit**, shared among multiple nodes via PCIe extender.

> Note: This tutorial focuses on **internal GPUs**, which are most common.

---

### 1.3 GPU Units

* A **chassis containing multiple GPUs**.
* Less common and require additional configuration.
* Example: Dell PowerEdge C410x.

---

# 🔹 2. Configuring GPU Settings in BCM

You can configure GPUs via **cmsh** or **Bright View**.

### 2.1 cmsh Submode: `gpusettings`

* Enter **device mode** for a node:

```bash
cmsh
device use node001
gpusettings
```

* Add GPU configuration:

```bash
add nvidia 1-3 ; commit
```

* **GPU range options**:

  * Single: `3`
  * Range: `0-2`
  * All GPUs: `all` or `*`

* **Device settings override category settings**.

---

### 2.2 Category Mode for GPUs

* Apply GPU settings to multiple nodes:

```bash
category use default
gpusettings
add nvidia 1-3 ; commit
```

* Benefits:

  * Cluster-wide consistency
  * Easier updates
  * Categories can be **default**, `gpu-nodes`, etc.

---

# 🔹 3. NVIDIA GPU Settings

After setting GPU type, configure parameters:

| Parameter            | Description                                                                     |
| -------------------- | ------------------------------------------------------------------------------- |
| `clockspeeds`        | GPU and memory frequency (MHz)                                                  |
| `clocksyncboostmode` | GPU boosting (`enabled` / `disabled`)                                           |
| `computemode`        | Context mode (`Default`, `Exclusive thread`, `Exclusive process`, `Prohibited`) |
| `eccmode`            | ECC memory error checking (`enabled` / `disabled`)                              |
| `powerlimit`         | Upper power limit (min, max, default, or custom number)                         |
| `GPU range`          | Applies setting to all GPUs, single GPU, or range                               |

* Requires **NVIDIA drivers** for changes to take effect.
* Install **cuda-dcgm** to access NVIDIA GPU metrics.

---

# 🔹 4. AMD GPU Settings

* Configured similarly to NVIDIA GPUs.
* Supported AMD GPUs: Radeon series.
* Parameters:

| Parameter             | Description                                        |
| --------------------- | -------------------------------------------------- |
| `activitythreshold`   | GPU usage % required for clock changes (0–100)     |
| `fanspeed`            | Max fan speed (0–255)                              |
| `gpuclocklevel`       | GPU clock level (0–7)                              |
| `memoryclocklevel`    | Memory clock level (0–3)                           |
| `overdrivepercentage` | Overclocking (0–20%)                               |
| `powerplaymode`       | Performance mode (`high`, `low`, `auto`, `manual`) |
| `hysteresisup/down`   | Delay in ms for clock change                       |
| `gpu range`           | GPU slot(s) to configure                           |

* Use `status` to see **supported clocks and frequencies**.

---

# 🔹 5. Managing GPUs via Bright View

* Node path: `Devices > Nodes[node001] > Edit > Settings > GPU Settings`
* Category path: `Grouping > Node categories[default] > Edit > GPU Settings`
* Interface is **graphical** but mirrors cmsh configuration.

---

# 🔹 6. Integrating GPUs with HPC Workload Managers

### 6.1 Slurm

* Add GPUs as **generic resources** (gres):

```bash
sbatch --gres=gpu:1
sbatch --gres=mps:100
```

* Configure Slurm client in cmsh:

```bash
configurationoverlay
use slurm-client
roles
use slurmclient
genericresources
add gpu0
set name gpu
set file /dev/nvidia0
commit
```

* Repeat for multiple GPUs (`gpu1`, `gpu2`, …).

* **Oversubscription**:

```bash
wlm slurm
jobqueue
use defq
set oversubscribe yes:8
commit
```

* Allows multiple jobs per node.

---

### 6.2 PBS

* BCM 9+ supports GPU via **cm-wlm-setup**.
* Automatically configures GPU devices after installation.

---

### 6.3 UGE (Univa Grid Engine)

* BCM 8.x: Configure GPU devices in `cgroups_params` using `qconf -mconf <hostname>`.
* BCM 9+: Use `devices` parameter in `UGEClient` role:

```bash
category[dgx]->roles[ugeclient]
set gpus 8
set gpudevices gpu0[device=/dev/nvidia0,cuda_id=0]
append gpudevices gpu1[device=/dev/nvidia1,cuda_id=1]
commit
```

* DCGM port for UGE: Default `5555`, configurable in BCM 9+ via CMDaemon.

---

### 6.4 LSF

* Auto-detect GPU devices:

```bash
LSF_GPU_AUTOCONFIG=Y
```

* Enforce GPU resources:

```bash
wlm lsf
cgroups
append resourceenforce gpu
```

---

# 🔹 7. Summary

* **GPU configuration levels**:

  * Device → specific node
  * Category → group of nodes
* **NVIDIA vs AMD GPUs** → different parameters (`clockspeeds`, `ECC`, `PowerPlay`, etc.)
* **Integration with WLMs** (Slurm, PBS, UGE, LSF) enables **GPU scheduling and allocation**
* **cmsh commands** provide **automation, reproducibility, cluster-wide consistency**
* Bright View mirrors cmsh, allowing **GUI management**

