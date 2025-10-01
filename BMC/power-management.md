# **Complete Guide to Power Management in Bright Cluster Manager (BCM)**

---

## **1. PDU-Based Power Control**

**Use Case:** Any device with a power supply (nodes, switches, appliances).

**How It Works:** Each device is plugged into a PDU (Power Distribution Unit). BCM can turn the device on/off by controlling the PDU port.

**Steps:**

1. **Add PDU to BCM:**

   * Make sure the PDU is reachable over the network.
   * In Bright View: `Devices → [device] → Settings → PowerDistributionUnits → Add PDU ports`
   * In `cmsh`:

     ```bash
     device use node001
     set powerdistributionunits apc01:6 apc01:7
     set powercontrol apc
     commit
     ```

2. **Power Operations:**

   * Single node:

     ```bash
     device power -n node001 on
     device power -n node001 off
     device power -n node001 reset
     device power -n node001 status
     ```
   * Multiple nodes or racks with delay:

     ```bash
     device power on -n node001,node002 -d 0.1
     device power on -p 3 rack[01-12]   # Power 3 racks at a time
     ```

**Notes:**

* Dumb PDUs (cannot be managed remotely) cannot return a status.
* Can assign multiple ports to one node if multiple power feeds exist.

---

## **2. IPMI-Based Power Control**

**Use Case:** Node devices with BMC (common in blade servers).

**How It Works:** BCM uses the node’s baseboard management controller to control power.

**Steps:**

1. **Setup network and authentication** for IPMI (see BCM network setup, Section 3.7).
2. **Set nodes to use IPMI:**

   ```bash
   device foreach -t physicalnode (set powercontrol ipmi0; commit)
   ```
3. **Perform power operations:**

   ```bash
   device power -n node001 on
   device power -n node001 off
   device power -n node001 reset
   device power -n node001 status
   ```

**Tip:** Can combine IPMI with PDU:

* Edit `/cm/local/apps/cmd/etc/cmd.conf`:

  ```ini
  PowerOffPDUOutlet = true
  ```
* After powering off a node, CMDaemon will also turn off the PDU port.

---

## **3. HP iLO-Based Power Control**

**Use Case:** HP servers with iLO interface.

**Steps:**

1. **Install iLO tools (`hponcfg`)** on head node, node image, and node installer:

   ```bash
   rpm -iv hponcfg-3.1.1-0.noarch.rpm
   rpm --root /cm/images/default-image -iv hponcfg-3.1.1-0.noarch.rpm
   rpm --root /cm/node-installer -iv hponcfg-3.1.1-0.noarch.rpm
   ```

2. **Set nodes to use iLO:**

   ```bash
   device foreach -c default (set powercontrol ilo0)
   device commit
   ```

3. **Power operations** are the same as IPMI:

   ```bash
   device power -n node001 on/off/reset/status
   ```

---

## **4. Dell DRAC-Based Power Control**

**Use Case:** Dell servers with DRAC management interface.

**Steps:**

1. Configure network and authentication for DRAC.
2. Set node power control to DRAC:

   ```bash
   device use node001
   set powercontrol drac0
   commit
   ```
3. Perform **power operations** same as IPMI or iLO.

---

## **5. Redfish / CIMC-Based Power Control**

**Use Case:** Cisco UCS servers or servers with Redfish API.

**Steps:**

1. Configure network and authentication for Redfish/CIMC.
2. Set node power control:

   ```bash
   device use node001
   set powercontrol redfish0   # For Cisco, use cimc0
   commit
   ```
3. Perform **power operations** as before:

   ```bash
   device power -n node001 on/off/reset/status
   ```

---

## **6. Custom Power Control Scripts**

**Use Case:** Devices not covered by PDU, IPMI, iLO, DRAC, or Redfish.

**Steps:**

1. **Create a custom script** on head node (example in Bash):

   ```bash
   #!/bin/bash
   operation=$1
   device=$2

   case $operation in
       ON) wakeonlan $CMD_MAC ;;   # Or $3 if MAC passed as argument
       OFF) ssh root@$device "poweroff" ;;
       RESET) ssh root@$device "reboot" ;;
       STATUS) ping -c1 $device >/dev/null && echo "ON" || echo "OFF" ;;
   esac
   ```

2. **Configure node to use custom script:**

   ```bash
   device use node001
   set powercontrol custom
   set custompowerscript /cm/local/examples/cmd/custompower
   commit
   ```

3. **Optional argument:**

   ```bash
   set custompowerscriptargument <value>
   ```

4. **Power operations** same as above:

   ```bash
   device power -n node001 on/off/reset/status
   ```

---

## **7. Scheduling Power Operations**

**Options in `cmsh`:**

* **Delay between nodes:** `-d`

  ```bash
  device power off -c default -d 0.1
  ```

* **Parallel batch:** `-p`

  ```bash
  device power on -p 3 rack[01-12]
  ```

* **Schedule after a time:** `--after`

  ```bash
  device power off --after 10m node001
  ```

* **Schedule at a specific time:** `--at`

  ```bash
  device power off --at 23:55 node001
  ```

* **Cancel scheduled operation:**

  ```bash
  device power cancel node001
  ```

* **Check pending operations:**

  ```bash
  device power wait
  device power list
  ```

---

## **8. Monitoring Power**

**Metrics from PDUs:**

* `PDUBankLoad`: Phase load (Amps)

* `PDULoad`: Total load (Amps)

* Can visualize in BCM monitoring dashboard (Chapter 13).

---

## **9. Switch Considerations**

* Enable **dynamic speed negotiation** on switches.
* Prevents nodes from losing connectivity after power cycles or Wake-on-LAN.

---

## **10. Quick cmsh Cheat Sheet**

```bash
# Power on/off/reset a single node
device power -n node001 on
device power -n node001 off
device power -n node001 reset

# Check power status
device power -n node001 status

# Power on multiple nodes with delay
device power -n node001,node002 -d 0.1 on

# Power on racks in batches of 3
device power on -p 3 rack[01-12]

# Schedule power off after 10 minutes
device power off --after 10m node001

# Cancel scheduled operations
device power cancel node001

# Check power history
device power history

# Use a custom script
device use node001
set powercontrol custom
set custompowerscript /cm/local/examples/cmd/custompower
commit
device power -n node001 on
```

---

✅ This guide gives **every possible way to manage power** in BCM, explained simply and step-by-step. It covers:

* **PDU, IPMI, iLO, DRAC, Redfish/CIMC, Custom scripts**
* **Power commands, batch operations, delays, scheduling**
* **Monitoring and best practices**
