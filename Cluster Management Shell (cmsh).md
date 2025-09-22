- cmsh is the command-line interface for managing clusters in Bright Cluster Manager. It provides full access to cluster management functionality, similar to Bright View (the web interface).

- Purpose of cmsh
  - Administrative Tool: Allows cluster administrators to manage all aspects of the cluster via the command line.
  - Efficiency-Oriented: Commands are designed for efficient cluster management rather than full programming capabilities.
  - Alternative Interfaces:
    - Bright View: Graphical interface for cluster management.
    - cmsh: Command-line interface, useful for scripts and batch operations.
  - Administrators who only use Bright View do not need to learn cmsh.

- Features
  - Interactive Mode: Run commands interactively (e.g., via ssh on the head node).
  - Batch Mode: Can execute cmsh commands in scripts or automated workflows.
  - Cluster Management Focus: Commands are tailored to tasks such as:
    - Node configuration
    - Software/module management
    - Job scheduling and monitoring
    - User and resource management
  - For programmatic cluster management, Bright recommends using Python bindings rather than relying on cmsh batch scripts.
 
- Invoke cmsh
  - From an interactive session on the head node:
    ```
    ssh admin@headnode
    cmsh
    ```

- 2 modes
  - invoke
    ```
    cmsh
    quit
    ```
  - batch (-c to get into batch mode, ; to give multiple command in batch mode)
    ```
    cmsh -c "<command>"
    ```
    ```
    cmsh -c "main showprofile; device status apc01"
    ```
    ```
     echo device status | cmsh
    ```

### Dotfiles and /etc/cmshrc
- cmsh loads dotfiles on startup (interactive and batch):
- Dotfile Path	Description
  - ~/.cm/cmsh/.cmshrc	Highest priority
  - ~/.cm/.cmshrc	Overrides /etc/cmshrc
  - ~/.cmshrc	Overrides /etc/cmshrc
  - /etc/cmshrc	Default system-wide settings
- Shortest path overrides: user dotfiles override /etc/cmshrc

---
---

## **1. What are Command Aliases in `cmsh`?**

A **command alias** is a shortcut for a longer `cmsh` command or sequence of commands. Aliases are useful to save time and avoid typing long commands repeatedly.

* Example: Instead of typing `device list virtualnode` every time, you can create an alias `lv`.
* Aliases can be defined **temporarily during a session** or **persistently using dotfiles**.

---

## **2. Defining Aliases**

### **2.1 Using `.cmshrc` (Persistent Aliases)**

You can define aliases in your `~/.cmshrc` file. This file is sourced automatically when `cmsh` starts.

**Example:**

```bash
# ~/.cmshrc
alias lv device list virtualnode
```

* Now, inside `cmsh`, typing `lv` will execute `device list virtualnode`.

---

### **2.2 Using the `alias` command inside `cmsh` (Temporary or Session Aliases)**

Aliases can also be defined **interactively** within a `cmsh` session.

**Example:**

```text
[mycluster]% alias lv device list virtualnode
[mycluster]% lv
```

* To **list all currently defined aliases**, use:

```text
[mycluster]% alias
```

---

### **2.3 Exporting and Importing Aliases**

Aliases can be exported to a file along with other cmsh dot settings, and then imported later.

**Exporting:**

```text
[mycluster]% export > /root/mydotsettings
```

**Importing:**

```text
[mycluster]% run /root/mydotsettings
```

This allows you to **reuse aliases across sessions**.

---

## **3. Built-in Aliases in `cmsh`**

`cmsh` also comes with **predefined aliases**. These are always available, no need to define them in `.cmshrc`.

| Alias | Command it represents | Purpose                                            |
| ----- | --------------------- | -------------------------------------------------- |
| `-`   | `goto -`              | Go to the previous directory level in `cmsh`       |
| `..`  | `exit`                | Exit current level or leave `cmsh` if at top level |
| `/`   | `home`                | Go to the top-level directory in `cmsh`            |
| `?`   | `help`                | Show help text for the current level               |
| `ds`  | `device status`       | Show status of accessible devices                  |
| `ls`  | `list`                | List items in the current context                  |

---

## **4. Automatic Aliases via `.cmsh` Scripts**

`cmsh` allows creating **automatic aliases** by placing scripts in `~/.cm/cmsh/` with a `.cmsh` extension. The filename becomes the alias automatically.

### **Example 1: Table Listing**

Create a file `/root/.cm/cmsh/tablelist.cmsh`:

```bash
list -d "|"
```

* Alias `tablelist` is automatically created.
* Runs `list` with `|` as delimiter.

### **Example 2: Shell Command (`df -h`)**

Create a file `/root/.cm/cmsh/dfh.cmsh`:

```bash
!df -h
```

* Alias `dfh` is automatically created.
* Runs the Linux shell command `df -h` from within `cmsh`.

### **Usage in `cmsh`**

```text
[mycluster->device]% tablelist
Type |Hostname (key) |MAC |Category |Ip
----------------------|----------------|------------------|----------------|---------------
HeadNode |mycluster |FA:16:3E:B4:39:DB | |10.141.255.254
PhysicalNode |node001 |FA:16:3E:D5:87:71 |default |10.141.0.1
PhysicalNode |node002 |FA:16:3E:BE:05:FE |default |10.141.0.2

[mycluster->device]% dfh
Filesystem Size Used Avail Use% Mounted on
/dev/vdb1 25G 17G 8.7G 66% /
tmpfs 1.9G 33M 1.8G 2% /run
```

> **Note:** For Bright Cluster Manager 9.1+, aliases created this way **do not require restarting `cmsh`** to become active.

---

## **5. Summary of Alias Options**

| Method                           | Persistence | How to Use                               |
| -------------------------------- | ----------- | ---------------------------------------- |
| `.cmshrc` file                   | Permanent   | Aliases are available in all sessions    |
| `alias` command in session       | Temporary   | Only available for current session       |
| `.cmsh` scripts in `~/.cm/cmsh/` | Permanent   | Filename becomes the alias automatically |
| `export` / `run`                 | Permanent   | Save and load multiple aliases at once   |


---
---

  
