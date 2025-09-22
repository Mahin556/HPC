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

