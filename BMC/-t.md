Ah! You’re asking about the `-t` option in **cmsh power/device commands**. Let me explain clearly.

---

## **`-t` Option in cmsh**

**Meaning:** Specifies the **type of devices** you want to select for the command.

**Syntax:**

```bash
-t, --type <type>
```

**Common values:**

* `node` → physical nodes
* `virtualnode` → virtual machines managed by BCM
* `cloudnode` → cloud instances
* `switch` → network switches (if BCM is managing them)
* `storage` → storage devices

**Example Usage:**

1. Power on **all physical nodes** in the cluster:

```bash
device power -t node on
```

2. Power off **all virtual machines**:

```bash
device power -t virtualnode off
```

3. Check power status for **cloud nodes**:

```bash
device power -t cloudnode status
```

---

**Notes:**

* `-t` can be combined with other filters like `-n`, `-g`, `-c`, etc.

  ```bash
  device power -t node -c default -d 0.1 on
  ```

  Powers on all nodes in the `default` category with 0.1s delay between nodes.

* Useful if your cluster has **mixed device types** (physical + virtual + cloud).

---

If you want, I can make a **full table of all cmsh power command options with examples** including `-t`, `-d`, `-p`, `--after`, etc., so you can use it as a ready reference.

Do you want me to make that?
