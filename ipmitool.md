Got it 👍 — you want a **complete reference of `ipmitool` commands**.
Since `ipmitool` is very broad (covering power, sensors, SDR, SEL, FRU, user management, LAN config, SOL, etc.), I’ll give you a **structured master list of all commands/subcommands with explanations** so you can use it like a cheat-sheet.

---

# 🖥️ `ipmitool` Master Command Reference

⚡ General syntax:

```bash
ipmitool [options] <command> [subcommand] [arguments]
```

### 🔧 Common Options

```bash
-I lanplus       # Interface (e.g., lan, lanplus, open, etc.)
-H <host>        # Remote BMC host/IP
-U <user>        # Username
-P <password>    # Password
-p <port>        # Remote port
-C <cipher>      # Cipher suite
```

---

## 📋 Global Help

```bash
ipmitool help
```

---

## 🔌 1. Power Management

```bash
ipmitool power status          # Show current power state
ipmitool power on              # Power on server
ipmitool power off             # Power off server
ipmitool power cycle           # Hard reset (off then on)
ipmitool power reset           # Soft reset (reboot)
ipmitool power diag            # Power-on self test
ipmitool power soft            # Graceful shutdown (ACPI)
```

---

## 🌡️ 2. Sensor Data (SDR)

```bash
ipmitool sdr                   # List all sensors
ipmitool sdr list              # Same as above
ipmitool sdr elist             # Extended info
ipmitool sdr get <id>          # Details of one sensor
ipmitool sdr type <type>       # Filter by type (e.g., Temperature, Fan, Voltage)
ipmitool sensor                # Alias for sdr elist
ipmitool sensor get <id>
```

---

## 📝 3. System Event Log (SEL)

```bash
ipmitool sel list              # Show log entries
ipmitool sel elist             # Extended view
ipmitool sel time get          # Show BMC SEL clock
ipmitool sel time set <time>   # Set clock
ipmitool sel clear             # Clear log
ipmitool sel delete <id>       # Delete entry
ipmitool sel info              # Info about SEL
```

---

## 📦 4. FRU (Field Replaceable Unit)

```bash
ipmitool fru                   # List FRU devices
ipmitool fru print             # Show FRU info
ipmitool fru read <id> <file>  # Dump FRU to file
ipmitool fru write <id> <file> # Write FRU from file
```

---

## 🌐 5. LAN / Network Config

```bash
ipmitool lan print <channel>                       # Show LAN config
ipmitool lan set <channel> ipaddr <x.x.x.x>        # Set IP
ipmitool lan set <channel> netmask <x.x.x.x>       # Set netmask
ipmitool lan set <channel> defgw ipaddr <x.x.x.x>  # Set gateway
ipmitool lan set <channel> arp respond on|off      # ARP
ipmitool lan set <channel> access on|off           # Enable/disable channel
ipmitool lan set <channel> auth <level> <types>    # Set authentication
```

---

## 👤 6. User Management

```bash
ipmitool user list <channel>                # Show users
ipmitool user set name <id> <username>      # Set username
ipmitool user set password <id> <password>  # Set password
ipmitool user enable <id>                   # Enable user
ipmitool user disable <id>                  # Disable user
ipmitool channel setaccess <chan> <id> callin=on ipmi=on link=on privilege=4
```

---

## 🎮 7. SOL (Serial-over-LAN)

```bash
ipmitool sol info               # SOL configuration
ipmitool sol set enabled true   # Enable SOL
ipmitool sol activate           # Start SOL session
ipmitool sol deactivate         # Stop SOL session
```

---

## 🔒 8. Chassis Commands

```bash
ipmitool chassis status          # Status
ipmitool chassis identify <time> # Blink chassis ID LED
ipmitool chassis bootdev pxe     # Next boot PXE
ipmitool chassis bootdev disk    # Next boot HDD
ipmitool chassis bootdev cdrom   # Next boot CD
ipmitool chassis bootdev bios    # Boot into BIOS setup
```

---

## 🛠️ 9. BMC Management

```bash
ipmitool mc info                 # BMC info
ipmitool mc guid                 # BMC GUID
ipmitool mc reset cold           # Hard reset BMC
ipmitool mc reset warm           # Soft reset BMC
ipmitool mc selftest             # Run BMC self-test
```

---

## 📡 10. Channel Info

```bash
ipmitool channel info <chan>     # Show channel info
ipmitool channel authcap <chan> <level> # Auth capabilities
ipmitool channel getaccess <chan> <id>
```

---

## 🔗 11. Session

```bash
ipmitool session info all
ipmitool session activate
ipmitool session close <id>
```

---

## 🔑 12. Certificate (TLS/SSL/IPMI v2)

```bash
ipmitool certificate list
ipmitool certificate import <file>
ipmitool certificate delete <id>
```

---

## 🔧 13. Miscellaneous

```bash
ipmitool time get                # Get current BMC time
ipmitool time set "09/22/2025 21:30:00"   # Set BMC time
ipmitool pef info                # Platform Event Filtering info
ipmitool pef status
ipmitool pef policy list
ipmitool event <data>            # Inject event
ipmitool raw <netfn> <cmd> [...] # Send raw IPMI command
```

---

# ⚡ Master Command List (Quick View)

* `power` → power control
* `sdr` / `sensor` → sensor readings
* `sel` → event logs
* `fru` → hardware inventory
* `lan` → network setup
* `user` / `channel` → user & privilege management
* `sol` → serial-over-LAN
* `chassis` → chassis control (boot/devices/LED)
* `mc` → BMC management
* `session` → session handling
* `certificate` → TLS certs
* `pef` → event filtering
* `event` / `raw` → advanced/low-level

---

```
ipmitool -I lanplus -H 192.168.1.100 -U admin -P password chassis status

ipmitool -I lanplus -H 10.0.0.5 -U admin -P secret chassis power status

ipmitool -I lanplus -H 10.0.0.5 -U admin -P secret chassis power cycle

ipmitool lan print 1

impitool fru print
```
