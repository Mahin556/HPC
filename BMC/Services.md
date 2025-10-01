# 🔹 1. Concepts You Must Know

### 1.1 What is a “Device” in BCM?

* A **device** is a **single node** in the cluster, which could be:

  * Head node (manages the cluster)
  * Compute node
* Device mode in `cmsh` allows you to configure services **per node**.
* Useful when you need **custom behavior** for a specific node.

---

### 1.2 What is a “Category” in BCM?

* A **category** is a **group of nodes**.

  * Example: `default` category might contain all compute nodes.
  * You can also create categories like `gpu-nodes`, `highmem-nodes`.
* Category mode in `cmsh` allows you to **apply the same service settings to multiple nodes at once**.
* Benefit: **centralized configuration** → less repetition, easier cluster-wide updates.

---

# 🔹 2. Ways to Configure a Service in cmsh

You can configure services at **two levels**:

| Level            | Mode in cmsh                                              | Use Case / Benefit                                        |
| ---------------- | --------------------------------------------------------- | --------------------------------------------------------- |
| **Device**       | `device services <node>`                                  | Customize service behavior for a single node              |
| **Category**     | `category services <category>`                            | Apply service settings to all nodes in a category at once |
| **Cluster-wide** | By configuring services on the category used by all nodes | Quick management of multiple nodes without repetition     |

---

# 🔹 3. Step-by-Step cmsh Tutorial for Services

---

## 3.1 Start cmsh

```bash
cmsh
```

You will enter the cmsh prompt:

```
[bright91]%
```

---

## 3.2 Configure a Service on a Single Node (Device Mode)

1. Enter **device mode**:

```bash
device node001
```

2. Enter **services submode**:

```bash
services
```

3. Add a service (example: `cups`):

```bash
add cups
```

4. View current service parameters:

```bash
show
```

Output example:

```
Autostart   no
Monitored   no
Run if      ALWAYS
Service     cups
```

---

### 3.2.1 Configure Parameters

| Parameter         | What it does                                               | Example Usage             |
| ----------------- | ---------------------------------------------------------- | ------------------------- |
| `monitored`       | BCM checks if service is running periodically              | `set monitored on`        |
| `autostart`       | Automatically restart service if it fails                  | `set autostart on`        |
| `runif`           | For high-availability nodes: determines where service runs | `set runif ACTIVE`        |
| `belongs_to_role` | Whether the service is tied to a node role                 | `set belongs_to_role yes` |

---

### 3.2.2 Apply Changes

```bash
commit
```

* Starts service if `autostart` is on and it’s not running.
* Logs the action in BCM.

---

### 3.2.3 Manage the Service

```bash
start       # starts service
stop        # stops service
restart     # restarts service
status      # shows current state: UP, STOPPED, DOWN, FAILING
reset       # clears FAILING state, attempts immediate restart
```

---

## 3.3 Configure a Service on a Category (Multiple Nodes)

1. Enter **category mode**:

```bash
category
services default
```

> `default` is the default category. You can also create new ones:

```bash
add mygpu
```

2. Add a service to the category:

```bash
add cups
```

3. Set parameters:

```bash
set monitored yes
set autostart yes
commit
```

4. Check status across all nodes in the category:

```bash
status
```

Output example:

```
node001    cups [UP]
node002    cups [UP]
node003    cups [STOPPED]
```

**Benefits of Category Mode**:

* Manage **multiple nodes at once**.
* Consistent configuration across similar nodes.
* Easier to apply updates later (just change category, all nodes inherit).

---

# 🔹 4. Service Parameters in Detail

| Parameter         | Description                                                                           |
| ----------------- | ------------------------------------------------------------------------------------- |
| `monitored`       | BCM checks if the service is running and logs start/stop events                       |
| `autostart`       | Restart service automatically if it fails                                             |
| `runif`           | (High availability) Decide where service runs: ACTIVE, PASSIVE, ALWAYS, PREFERPASSIVE |
| `belongs_to_role` | Assign service to a specific node role                                                |
| `state`           | Current service state: UP, STOPPED, DOWN, FAILING                                     |
| `commit`          | Apply the configuration changes                                                       |

---

# 🔹 5. Removing a Service

1. Remove with **autostart ON** → service stops automatically:

```bash
remove cups
commit
```

2. Remove with **autostart OFF** → service keeps running but is no longer monitored:

```bash
set autostart off
commit
remove cups
commit
```

---

# 🔹 6. Monitoring Services

* Services are monitored continuously if `monitored` is ON.

* Service states:

  * **UP** → running
  * **STOPPED** → manually stopped
  * **DOWN** → service not running
  * **FAILING** → service could not start after 10 autostart attempts

* Check cluster health:

```bash
latesthealthdata
```

* BCM flag `ManagedServicesOk` turns **FAIL** if any monitored service is failing.

---

# 🔹 7. Why Use cmsh Instead of Just Linux Commands?

| Feature                                 | Linux Shell | cmsh                            |
| --------------------------------------- | ----------- | ------------------------------- |
| Node-specific management                | ✅           | ✅                               |
| Category-wide / cluster-wide management | ❌           | ✅                               |
| Auto-restart on failure                 | ❌           | ✅ (autostart)                   |
| Monitoring with logging                 | ❌           | ✅ (monitored)                   |
| Integration with cluster HA             | ❌           | ✅ (runif + failover scripts)    |
| GUI access via Bright View              | ❌           | ✅ (cmsh changes reflect in GUI) |

---

# 🔹 8. Bonus: Resetting a Service in FAILING State

```bash
reset
```

* Resets autostart attempts counter
* Forces immediate restart
* Useful if service is temporarily failing and BCM has marked it as FAILING

---

# 🔹 9. Practical Exercises

1. **Device-level practice**:

   * Add `cups` to node001
   * Set `monitored on`, `autostart on`
   * Stop service → observe auto-restart
   * Check `status` and `latesthealthdata`

2. **Category-level practice**:

   * Add `cups` to category `default`
   * Apply `monitored on` and `autostart on`
   * Stop service on node002 → verify others stay up
   * Remove service with autostart OFF → verify it keeps running but is unmanaged

3. **High Availability Practice**:

   * Set `runif ACTIVE` for a service
   * Simulate failover → service runs only on active node
