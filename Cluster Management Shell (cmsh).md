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

## **1. Default Arguments in `cmsh` Scripts**

In `cmsh` scripts, you can pass **positional parameters** using `$1`, `$2`, etc., similar to shell scripting.

### **1.1 Default Values for Parameters**

* If a parameter is not provided when the script is run, it can take a **default value**.
* The syntax for default values is:

```text
${<parameter>-<default_value>}
```

* If the argument is blank or not passed, the parameter uses `<default_value>`.

---

### **1.2 Example Script: `encrypt-node-disk.cmsh`**

```bash
home
device use ${1-node001}
set disksetup /root/my-encrypted-node-disk.xml
set revision ${2-test}
commit
```

* `$1` → Node name (default: `node001`)
* `$2` → Revision name (default: `test`)

---

### **1.3 Running the Script**

**Without arguments:**

```bash
cmsh
[mycluster]% encrypt-node-disk
[mycluster->device[node001]]%
```

* Uses `node001` as the default node.

**With arguments:**

```bash
cmsh
[mycluster]% encrypt-node-disk node002
[mycluster->device[node002]]%
```

* `$1` becomes `node002`.

---

### ✅ **Key Points**

1. Blank arguments do not cause errors; defaults are used if specified.
2. Supports flexible scripting: you can pass one, both, or no arguments.
3. Works similarly to shell scripting default variables.

---

## **2. Command-Line Options for `cmsh`**

`cmsh` can be run with **options** to modify behavior or run commands non-interactively.

### **2.1 Usage Syntax**

```text
cmsh [options] [hostname[:port]]
cmsh [options] -c <command>
cmsh [options] -f <filename>
```

---

### **2.2 Common Options**

| Option                        | Description                           |
| ----------------------------- | ------------------------------------- |
| `--help` / `-h`               | Display help text                     |
| `--noconnect` / `-u`          | Start without connecting to a cluster |
| `--controlflag` / `-z`        | ETX in non-interactive mode           |
| `--color <yes/no>`            | Enable or disable color output        |
| `--spool <directory>`         | Alternative spool directory           |
| `--tty` / `-t`                | Pretend a TTY is available            |
| `--noredirect` / `-r`         | Do not follow redirects               |
| `--norc` / `-n`               | Do not load `.cmshrc` file on startup |
| `--noquitconfirmation` / `-Q` | Do not ask for quit confirmation      |
| `--echo` / `-x`               | Echo all commands before executing    |
| `--quit` / `-q`               | Exit immediately after an error       |
| `--disablemultiline` / `-m`   | Disable multiline support             |
| `--hide-events`               | Hide all events by default            |
| `--disable-events`            | Disable all events by default         |

---

### **2.3 Arguments**

* `hostname` → The node or cluster to connect to.
* `command` → Command(s) to execute.
* `filename` → File containing a list of `cmsh` commands.

---

### **2.4 Example Usages**

**Interactive mode:**

```bash
cmsh
```

**Run a single command and exit:**

```bash
cmsh -c 'device status'
```

**Run a command without showing events:**

```bash
cmsh --hide-events -c 'device status'
```

**Run commands from a file, echo them, and exit on error:**

```bash
cmsh -f some.file -q -x
```

---

### **3. Additional Notes**

* `cmsh` has a `man` page (`man cmsh`) which is more extensive than `-h`.
* The man page covers options and arguments but does **not fully describe interactive behavior**.
* Default arguments and `.cmsh` scripts can be combined to make powerful, reusable automation scripts.

---
---


## **1. `--help` / `-h`**

Display help text.

```bash
cmsh --help
cmsh -h
```

---

## **2. `--noconnect` / `-u`**

Start `cmsh` without connecting to a cluster.

```bash
cmsh --noconnect
cmsh -u
```

---

## **3. `--controlflag` / `-z`**

Enable ETX in non-interactive mode (used for scripts or automation).

```bash
cmsh --controlflag
cmsh -z
```

---

## **4. `--color <yes/no>`**

Enable or disable colored output.

```bash
cmsh --color yes
cmsh --color no
```

---

## **5. `--spool <directory>`**

Specify an alternative spool directory.

```bash
cmsh --spool /tmp/myspool
```

---

## **6. `--tty` / `-t`**

Pretend a TTY is available (useful for scripts that require interactive mode).

```bash
cmsh --tty
cmsh -t
```

---

## **7. `--noredirect` / `-r`**

Prevent `cmsh` from following redirects.

```bash
cmsh --noredirect
cmsh -r
```

---

## **8. `--norc` / `-n`**

Do **not** load `.cmshrc` file on startup.

```bash
cmsh --norc
cmsh -n
```

---

## **9. `--noquitconfirmation` / `-Q`**

Do not ask for confirmation when quitting.

```bash
cmsh --noquitconfirmation
cmsh -Q
```

---

## **10. `--echo` / `-x`**

Echo all commands before executing (useful for debugging).

```bash
cmsh --echo
cmsh -x
```

---

## **11. `--quit` / `-q`**

Exit immediately after an error occurs.

```bash
cmsh --quit
cmsh -q
```

---

## **12. `--disablemultiline` / `-m`**

Disable multi-line command support.

```bash
cmsh --disablemultiline
cmsh -m
```

---

## **13. `--hide-events`**

Hide all events by default during the session.

```bash
cmsh --hide-events
```

---

## **14. `--disable-events`**

Disable all events by default.

```bash
cmsh --disable-events
```

---

## **15. `-c <command>`**

Run a single command and exit.

```bash
cmsh -c "device status"
cmsh --color yes -c "device list virtualnode"
```

---

## **16. `-f <filename>`**

Run commands from a file and exit.

```bash
cmsh -f /root/myscript.cmsh
cmsh --color no -f /root/myscript.cmsh
```

---

## **17. `hostname[:port]`**

Connect to a specific cluster node.

```bash
cmsh node001
cmsh node001:6789
```

---

## **18. Combining Options**

You can combine multiple options for automation:

```bash
cmsh --hide-events -c "device status" -x
cmsh -n -u -t -f /root/myscript.cmsh
```

---
---


# **Cluster Management Shell (cmsh) – Levels, Modes, Help, and Commands**

---

## **1. Top-Level vs. Modes**

* The **top-level** is what you see when you start `cmsh` without any options:

```bash
cmsh
[mycluster]%
```

* To manage cluster resources efficiently, functionality is divided into **modes**. Each mode provides commands relevant to a specific aspect of cluster management.

**Examples of Modes:**

* `device` → Manage hardware devices like nodes, NICs, disks.
* `network` → Manage networks.
* `user` → Manage user accounts.
* `configurationoverlay` → Manage configuration overlays.
* `kubernetes` → Manage Kubernetes objects.

---

## **2. Objects Within Modes**

* Within a mode, you can operate on **objects** (specific entities like a node, user, or role).
* Use the `use` command to select an object:

```bash
[bright91->device]% use node001
[bright91->device[node001]]%
```

* You can then manage the object's properties, e.g., IP, MAC, category.

---

## **3. Navigation Commands**

| Command        | Purpose                                                             |
| -------------- | ------------------------------------------------------------------- |
| `exit` or `..` | Go up one level or exit cmsh if at top level                        |
| `home` or `/`  | Return to top-level from any depth                                  |
| `path`         | Show the current mode/object path (useful for scripting or aliases) |
| `use <object>` | Select an object within a mode                                      |

**Example:**

```bash
[bright91->device[node001]]% path
home;device;use node001;
```

* You can copy this path and use it in a bash shell with `cmsh -c`:

```bash
cmsh -c 'device;use node001; list'
```

---

## **4. Help System in cmsh**

* Typing `help` at any level gives two lists:

  1. **Top-level commands**
  2. **Commands available at the current mode**

**Example:**

```bash
[myheadnode]% session
[myheadnode->session]% help
============================ Top =============================
alias ......................... Set aliases
category ...................... Enter category mode
...
========================== session ===========================
id ....................... Display current session id
killsession .............. Kill a session
list ..................... List active sessions
```

* To get **detailed help for a specific command**:

```bash
[myheadnode]% help run
Name: run - Execute all commands in the given file(s)
Usage: run [OPTIONS] <filename> [<filename2> ...]
Options: -x, --echo
         -q, --quit
```

---

## **5. Command Syntax in cmsh**

* A **cmsh command line** can include:

```text
<mode> <cmd> <arg1> <arg2> ... ; <mode> <cmd> <arg1> ...
```

* Semi-colons (`;`) separate multiple commands.
* `<mode>` is optional if you are already inside that mode.

**Examples:**

```bash
# Execute device status for head node while in network mode
[bright91->network]% device status bright91; list
bright91 ............ [ UP ]
Name (key) Type Netmask Base Address ...
externalnet External 16 192.168.1.0 ...

# Enter device mode explicitly
[bright91->network]% device; status bright91; list
```

---

## **6. Top-Level Commands in cmsh**

* Typing `help` at top level lists commands such as:

| Command                  | Description                        |
| ------------------------ | ---------------------------------- |
| `alias`                  | Set or display aliases             |
| `device`                 | Enter device mode                  |
| `network`                | Enter network mode                 |
| `user`                   | Enter user mode                    |
| `list`                   | List objects or status             |
| `run`                    | Execute cmsh commands from file    |
| `exit` / `..`            | Exit mode or shell                 |
| `home` / `/`             | Return to top level                |
| `watch`                  | Execute command periodically       |
| `quit`                   | Quit shell                         |
| `connect` / `disconnect` | Connect or disconnect from cluster |
| `color`                  | Manage console text colors         |
| `events`                 | Manage events                      |

> Every mode also supports the **top-level commands**, e.g., `alias`, `help`, `exit`.

---

## **7. Examples of Navigation and Execution**

### **7.1 Entering a Mode and Using an Object**

```bash
[bright91]% device
[bright91->device]% use node001
[bright91->device[node001]]% list
```

### **7.2 Returning to Top Level**

```bash
[bright91->device[node001]]% home
[bright91]%
```

### **7.3 Executing Commands Non-Interactively**

```bash
# Copy-pasted from path output earlier
[bright91]% cmsh -c 'device;use node001; list'
```

### **7.4 Running Multiple Commands in One Line**

```bash
[bright91->network]% device status bright91; list
```

---

## **8. Summary Tips**

1. **Modes** = groups of commands for a specific area of cluster management.
2. **Objects** = specific entities within a mode, selected via `use`.
3. **`help`** shows available commands at top-level and mode-level.
4. **Navigation:** `exit`/`..` goes up one level; `home`/`/` goes to top-level.
5. **Non-interactive execution:** Use `cmsh -c` with the full mode/object path.

---
---


1. **Top-level commands** – available in any mode.
2. **Mode commands** – specific to each mode.
3. **Object commands** – available once you select an object within a mode.

Below is a detailed and organized reference for Bright Cluster Manager `cmsh` 9.1+.

---

# **1. Top-Level Commands (Available in Any Mode)**

| Command            | Purpose                                                |
| ------------------ | ------------------------------------------------------ |
| `alias`            | Set or display command aliases.                        |
| `unalias`          | Remove a previously defined alias.                     |
| `help [command]`   | Display help for top-level or mode-specific command.   |
| `exit` / `..`      | Exit current object or mode; at top-level, quits cmsh. |
| `home` / `/`       | Return to top-level prompt from any mode depth.        |
| `run`              | Execute commands from a file or string.                |
| `export`           | Export aliases and dotfile settings.                   |
| `list`             | List objects, devices, or status.                      |
| `history`          | Display command history.                               |
| `quit`             | Quit cmsh immediately.                                 |
| `quitconfirmation` | Manage quit confirmation behavior.                     |
| `time`             | Measure execution time of commands.                    |
| `watch`            | Execute a command periodically and display output.     |

---

# **2. Modes (Enter a Mode Using `mode-name`)**

Each mode contains **mode-specific commands** and may allow selection of objects.

| Mode                         | Purpose / Key Commands                                                            |
| ---------------------------- | --------------------------------------------------------------------------------- |
| `device`                     | Manage nodes, switches, disks, NICs.<br>Commands: `use <node>`, `status`, `list`  |
| `network`                    | Manage networks.<br>Commands: `list`, `status`, `connectivity`                    |
| `user`                       | Manage users.<br>Commands: `add`, `remove`, `modify`, `list`                      |
| `group`                      | Manage groups.<br>Commands: `add`, `remove`, `list`, `members`                    |
| `nodegroup`                  | Manage node groups.<br>Commands: `add`, `remove`, `list`, `members`               |
| `rack`                       | Manage racks.<br>Commands: `add`, `remove`, `list`, `nodes`                       |
| `session`                    | Manage active sessions.<br>Commands: `list`, `id`, `killsession`                  |
| `task`                       | Manage scheduled tasks.<br>Commands: `add`, `remove`, `list`, `run`               |
| `softwareimage`              | Manage software images.<br>Commands: `add`, `remove`, `list`, `assign`            |
| `profile`                    | Manage node/software profiles.<br>Commands: `add`, `remove`, `list`, `apply`      |
| `configurationoverlay`       | Manage configuration overlays.<br>Commands: `add`, `remove`, `list`, `roles`      |
| `ceph`                       | Manage Ceph clusters.<br>Commands: `status`, `list`, `mon`, `osd`                 |
| `etcd`                       | Manage etcd services.<br>Commands: `status`, `list`, `cluster`                    |
| `wlm`                        | Workload Manager (job scheduling).<br>Commands: `list`, `add`, `remove`, `status` |
| `monitoring`                 | Monitor cluster health.<br>Commands: `list`, `status`, `metrics`                  |
| `cloud`                      | Manage cloud resources.<br>Commands: `add`, `remove`, `list`, `status`            |
| `cmjob`                      | Manage cluster jobs.<br>Commands: `add`, `remove`, `list`, `status`               |
| `fspart`                     | Manage file system partitions.<br>Commands: `list`, `status`, `modify`            |
| `keyvaluestore`              | Key/value storage management.<br>Commands: `set`, `get`, `delete`, `list`         |
| `partition`                  | Job scheduling partitions.<br>Commands: `add`, `remove`, `list`                   |
| `edgesite`                   | Edge site management.<br>Commands: `add`, `remove`, `list`                        |
| `unmanagednodeconfiguration` | Manage unmanaged node configs.<br>Commands: `list`, `modify`                      |
| `time`                       | Measure command execution time.                                                   |
| `process`                    | Manage cluster processes.<br>Commands: `list`, `kill`, `status`                   |
| `hierarchy`                  | Explore object hierarchy.<br>Commands: `list`, `tree`                             |
| `groupingsyntax`             | Manage grouping defaults.<br>Commands: `list`, `modify`                           |
| `cert`                       | Manage certificates.<br>Commands: `add`, `remove`, `list`, `status`               |
| `main`                       | General management.<br>Commands: mode-specific and top-level commands             |

---

# **3. Object Commands (After `use <object>`)**

Once inside a mode and object:

| Command             | Purpose                                                  |
| ------------------- | -------------------------------------------------------- |
| `list`              | List properties or child objects of the selected object. |
| `status`            | Show the current status of the object.                   |
| `set <property>`    | Set a property of the object.                            |
| `commit`            | Commit changes made to the object.                       |
| `refresh`           | Refresh object data.                                     |
| `delete` / `remove` | Remove the object (mode-dependent).                      |
| `use <sub-object>`  | Select a child object for detailed management.           |
| `help`              | Display commands available for the object.               |

---

# **4. Examples of cmsh Commands**

### **4.1 Top-Level Command**

```bash
[bright91]% alias lv device list virtualnode
```

### **4.2 Enter a Mode**

```bash
[bright91]% device
[bright91->device]%
```

### **4.3 Use an Object**

```bash
[bright91->device]% use node001
[bright91->device[node001]]%
```

### **4.4 Run a Command Non-Interactively**

```bash
cmsh -c 'device;use node001; list'
```

### **4.5 Multiple Commands in One Line**

```bash
[bright91->network]% device status bright91; list
```

---

# **5. Notes**

1. **Top-level commands are global** – they work in any mode.
2. **Modes are hierarchical** – enter a mode to access its objects.
3. **Objects are selected via `use`** – after that, object-specific commands apply.
4. **Aliases, run, and export** make automation easier.

---
---

# **1. Object Lifecycle Commands**

| Command                    | Description                                                | Example                                                                 |                                |
| -------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------- | ------------------------------ |
| `use <object>`             | Make the specified object the current object.              | `[mycluster->device]% use node001`                                      |                                |
| `add <type> <object> [IP]` | Create a new object in the current mode and drop into it.  | `[mycluster->device]% add physicalnode node100 10.141.0.100`            |                                |
| `assign <object>`          | Assign an existing object to another object.               | `[...]->roles]% assign slurmclient`                                     |                                |
| `unassign <object>`        | Remove assignment of an object from another object.        | `[...]->roles]% unassign slurmclient`                                   |                                |
| `clear <property>`         | Reset a property of the object to its default.             | `[...->device*]% clear node101 mac`                                     |                                |
| `clone <source> <target>`  | Duplicate an object and drop into it.                      | `[mycluster->device]% clone node100 node101`                            |                                |
| `remove <object>`          | Mark an object for removal; not permanent until `commit`.  | `[mycluster->device]% remove node100`                                   |                                |
| \`commit \[-w              | --wait]\`                                                  | Save all local changes to the cluster; `-w` waits for background tasks. | `[mycluster->device*]% commit` |
| `refresh`                  | Undo local changes to the object.                          | `[mycluster->device*[node101*]]% refresh`                               |                                |
| `modified`                 | List objects with uncommitted changes.                     | `[mycluster->device*]% modified`                                        |                                |
| `validate`                 | Check consistency of object properties without committing. | `[mycluster->device*[node001*]]% validate`                              |                                |

---

# **2. Object Property Commands**

| Command                         | Description                                                     | Example                                                              |
| ------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------------------- |
| `get <property>`                | Display a specific property of the current object.              | `[...->device[node101]]% get category`                               |
| `set <property> <value>`        | Set a property of the current object.                           | `[...->device[node101*]]% set category default`                      |
| `append <property> <value>`     | Add a value to a multi-valued property.                         | `[...->device[node001*]]% append powerdistributionunits apc01:5`     |
| `removefrom <property> <value>` | Remove a value from a multi-valued property.                    | `[...->device[node001*]]% removefrom powerdistributionunits apc01:5` |
| `show`                          | Display all properties and their current values for the object. | `[mycluster->device[node001]]% show`                                 |

---

# **3. Object Display & Organization**

| Command                            | Description                                            | Example                                             |
| ---------------------------------- | ------------------------------------------------------ | --------------------------------------------------- |
| `list`                             | List all objects at the current mode or object level.  | `[mycluster->device]% list`                         |
| `sort <property1> [property2 ...]` | Sort the list output by specified properties.          | `[mycluster->device]% sort type hostname`           |
| `format <field1:width,...>`        | Specify which properties and widths to show in `list`. | `[bright91->device]% format hostname:[10-14],ip:15` |
| `usedby <object>`                  | Show which objects reference the specified object.     | `[mycluster->device]% usedby apc01`                 |

---

# **4. Advanced Object Commands**

| Command                              | Description                                    | Example                                                                         |
| ------------------------------------ | ---------------------------------------------- | ------------------------------------------------------------------------------- |
| `swap <object1> <object2>`           | Exchange names/attributes between two objects. | `[mycluster->device[mycluster]->interfaces]% swap eth0 eth1; commit`            |
| `import [options] <source> <target>` | Clone roles from another object or overlay.    | `[bright91->device[node001]->roles]% import --overlay slurm-client slurmclient` |
| `foreach <object-list> <commands>`   | Execute a set of commands on multiple objects. | `[mycluster->device]% foreach node001 node002 set category default`             |

---

# **5. Object Notes & Tips**

1. **Asterisk `*` in prompt** – indicates an object has unsaved changes.
2. **Commit sequence** – `add` → `set` → `commit` → object is persistent.
3. **Property types** – strings, booleans (`yes/1/on/true`, `no/0/off/false`), MAC, IP.
4. **List formatting** – flexible widths, ranges, and comma-separated multi-fields.
5. **Cloning** – assigns best-effort IP addresses; manual inspection recommended.
6. **Assign vs Add** – `assign` uses existing object, `add` creates a new object.
7. **Remove options** – `-d|--data` deletes underlying data, `-a|--all` deletes all revisions (softwareimage mode).

---
---


Here’s a structured summary and breakdown of the **advanced cmsh features** and cluster management techniques from the Bright Cluster Manager manual you shared. I’ve organized it for clarity so you can reference it quickly:

---

## **1. Cluster Partitioning & Base Partition**

* **Cluster partitions:** Virtual clusters inside a physical cluster. Currently only **`base`** exists.
* **Base partition:** Represents the entire physical cluster; contains global cluster properties.
* **Accessing base partition:**

```bash
cmsh
[myheadnode]% partition use base
[myheadnode->partition[base]]% show
```

* Shows cluster-wide properties like `Cluster name`, `Headnode`, `Management network`, `Time servers`, etc.

---

## **2. Command Line Editing & History**

* Uses **readline** library (similar to Bash):

  * Tab completion.
  * `<Ctrl>-r` or up/down arrows for history.
* **History command:**

```bash
history          # show command history
history -t       # show history with timestamps
```

* History saved in: `.cm/.cmshhistory`
* Convert Unix epoch timestamp:

```bash
date -d @1615412046
```

---

## **3. Mixing cmsh and Unix Commands**

* Execute shell commands from cmsh using `!`:

```bash
!hostname -f
!ssh node001
```

* Execute a command and use its output inside cmsh:

```bash
device use `hostname`
```

---

## **4. Output & Input Redirection**

* Redirect output:

```bash
device list > devices       # overwrite
device status >> devices    # append
device list | grep node001  # pipe to another command
```

* Redirect input from file:

```bash
cmsh < runthis
cmsh -f runthis   # suppress echo of commands
```

---

## **5. Time & Watch Commands**

* **time**: Measure execution time of a cmsh command:

```bash
time ds node001
```

* **watch**: Run command periodically:

```bash
watch newnodes
watch -n 3 status -n node001,node002
```

---

## **6. Looping Over Objects**

### **foreach**

* Iterate over objects with a list of commands:

```bash
foreach node001 node002 (get hostname; status)
```

* **Advanced options:**

  * Grouping: `-n`, `-g`, `-c`, `-r`, `-h`, `-l`, `-m`, `-u`, `-i`, `-t`
  * Adding: `--clone`, `--add`
  * Conditional: `--status`, `--quitonunknown`
  * Looping: `*`, `--verbose`

### **Node list syntax**

* Adhoc: `node001,node003,node005`
* Sequential: `node001..node004` or `node00[1-4]`
* Extended sequential: `node[001-002]s[001-005]`
* Rack-based: `r1n01,r1n02,r2n01`
* Exclusion: `node001..node005,-node002..node003`
* Stride: `node00[1..7:2]`
* Mixed: `r1n001..r1n003,r2n003`
* File: `^/some/filepath` (caret `^` reads nodes from a file)

---

## **7. Range Command**

* Modal looping over nodes (like `foreach`) with interactive mode:

```bash
range -n node0[01-24]
range -c default
get revision
set revision test
roles; assign pbsproclient; commit
```

* Can run `pexec` across a range, but generally avoided.

---

## **8. Bookmarks**

* Bookmarks = saved positions in cmsh hierarchy.
* Commands:

```bash
bookmark            # set current location
bookmark -l         # list bookmarks
goto <bookmark>     # go to a bookmark
bookmark -s mysaved # save bookmarks
bookmark -x mysaved # load bookmarks
```

* Special bookmark `-` = previous location.

---

## **9. Renaming Nodes**

* Simple rename:

```bash
rename node001..node003 test
commit
```

* Zero-padded numbers automatically if needed: `test01`, `test02`
* Format with custom digits: `test%04d`
* Exact mapping:

```bash
rename node[001-005] test0[1,2,5-7]
commit
```

* Skip nodes using colon `:` for patterns:

```bash
rename node[001-100:2] left[001-050]
rename node[002-100:2] right[001-050]
commit
```

* Dry-run: `--dry-run`
* Refresh clears pending changes.

---

## **10. CMDaemon Environment Variables**

* View environment variables for a device:

```bash
environment node001
```

* Export for bash scripts:

```bash
environment -e node001
```

* Example variables: `CMD_CLUSTERNAME`, `CMD_DEVICE_TYPE`, `CMD_CATEGORY`, `CMD_NODEGROUPS`, etc.

---

This covers **advanced cmsh usage, looping, range management, node renaming, and scripting with environment variables**.

If you want, I can also create a **quick cheat sheet with all commands, options, and examples** for **cmsh advanced features**, so you can use it as a one-page reference.

Do you want me to make that?

---

### To list all devices
```bash
cmsh
[mycluster]% device
[mycluster->device]% list
Type          Hostname       MAC                 Category
------------  -------------  -----------------  --------
PhysicalNode  node001        00:E0:81:2E:F7:96  default
PhysicalNode  node002        00:E0:81:2E:F7:97  default
...
```

### To get hostname of device/node
```bash
cmsh
[mycluster]% device
[mycluster->device]% get hostname node001
node001
```

### To get ip of device/node
```bash
cmsh
[mycluster]% device
[mycluster->device]% get ip node001
10.141.0.1
```

### Get hostname and IP together using foreach
```bash
foreach node001 node002 (get hostname; get ip)
node001
10.141.0.1
node002
10.141.0.2
```

### Get hostname and IP for all nodes
```bash
foreach * (get hostname; get ip)
foreach * (get hostname; get ip) > devices.txt
foreach * (get hostname; get ip; status)
```

```bash
# Start cmsh
[root@headnode ~]# cmsh

# Enter device mode for the head node
[bright91]% device use bright91

# Enter interfaces submode
[bright91->device[bright91]]% interfaces

# Select the interface for the internal network (eth1 in this example)
[bright91->device[bright91]->interfaces]% use eth1

# Add additional hostnames (space-separated)
[bright91->device[bright91]->interfaces[eth1]]% set additionalhostnames test host2 host3

# Commit changes to apply them
[bright91->device*[bright91*]->interfaces*[eth1*]]% commit

# Test the hostname resolution using ping (runs a shell command from cmsh)
[bright91->device[bright91]->interfaces[eth1]]% !ping test

This is done to manage hostnames and DNS for cluster nodes properly, rather than manually editing /etc/hosts. Here's why:

Centralized name resolution

Bright Cluster Manager (BCM) runs a DNS service on the internal network (internalnet).

Adding hostnames via additionalhostnames ensures that all nodes in the cluster can resolve the hostnames automatically.

If you edited /etc/hosts manually, you’d have to update it on every node, which is error-prone.

Automatic updates

When you commit in cmsh, the named (DNS) service is restarted automatically, so the new hostnames are immediately available.

Cluster integration

Any services, scripts, or jobs running on nodes can rely on consistent hostnames.

For example, if you ssh or ping a node using its hostname, it will always resolve to the correct internal IP.

Supports multiple hostnames per node

You can assign aliases or extra names for nodes without touching system files.

Example: test might be an alias for bright91.cm.cluster.

Avoid conflicts with CMDaemon-managed files

BCM manages parts of /etc/hosts automatically. Editing it manually can break cluster services. Using additionalhostnames keeps changes safe and supported.

✅ In short: it’s about automated, consistent, and cluster-wide hostname management.
```


```bash
[root@bright91 ~]# cmsh
[bright91]% device use bright91
[bright91->device[bright91]]% set hostname foobar
[foobar->device*[foobar*]]% commit
[foobar->device[foobar]]%
Tue Jan 22 17:35:29 2013 [warning] foobar: Reboot required: Hostname changed
[foobar->device[foobar]]% quit
[root@bright91 ~]# sleep 30; hostname -f foobar.cm.cluster
[root@bright91 ~]#
```

```bash
[root@mycluster ~]# cat /root/.cm/cmsh/tablelist.cmsh
list -d "|"
[root@mycluster ~]# cat /root/.cm/cmsh/dfh.cmsh
!df -h
[root@mycluster ~]# cmsh
[mycluster]% device
[mycluster->device]% alias | egrep '(tablelist|dfh)'
alias dfh run /root/.cm/cmsh/dfh.cmsh
alias tablelist run /root/.cm/cmsh/tablelist.cmsh
[mycluster->device]% list
Type Hostname (key) MAC Category Ip ---------------------- ---------------- ------------------ ---------------- ---------------HeadNode mycluster FA:16:3E:B4:39:DB 10.141.255.254 PhysicalNode node001 FA:16:3E:D5:87:71 default 10.141.0.1 PhysicalNode node002 FA:16:3E:BE:05:FE default 10.141.0.2 [mycluster->device]% tablelist
Type |Hostname (key) |MAC |Category |Ip ----------------------|----------------|------------------|----------------|---------------HeadNode |mycluster |FA:16:3E:B4:39:DB | |10.141.255.254 PhysicalNode |node001 |FA:16:3E:D5:87:71 |default |10.141.0.1 PhysicalNode |node002 |FA:16:3E:BE:05:FE |default |10.141.0.2 [mycluster->device]% dfh
Filesystem Size Used Avail Use% Mounted on
devtmpfs 1.8G 0 1.8G 0% /dev
tmpfs 1.9G 0 1.9G 0% /dev/shm
tmpfs 1.9G 33M 1.8G 2% /run
tmpfs 1.9G 0 1.9G 0% /sys/fs/cgroup
/dev/vdb1 25G 17G 8.7G 66% /
tmpfs 374M 0 374M 0% /run/user/0
```

