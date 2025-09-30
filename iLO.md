* **What iLO is**

  * iLO is Hewlett Packard Enterprise’s out-of-band management controller.
  * Like Dell’s iDRAC, it lets administrators manage and monitor HPE ProLiant servers remotely.
  * It works even if the OS is crashed, the network is down, or the server is powered off (as long as it has standby power).

* **How it works**

  * iLO is an embedded microcontroller (management chip) on the HPE ProLiant server’s system board.
  * It has its own network interface (dedicated port) and can also share the main NIC in shared mode.
  * It runs independently of the main OS and gives you remote control via browser, SSH, or CLI tools.
  * It provides **KVM over IP, virtual media, remote power, hardware health monitoring, and firmware updates**.

* **Where it is located on the server**

  * On the **motherboard of HPE ProLiant servers**.
  * Accessible through:

    * A **dedicated iLO port** (RJ-45, usually marked with “iLO” above it).
    * Or in shared NIC mode using one of the main LAN ports.

* **How you interact with it**

  * When the server boots, you can press **F8** to configure iLO settings.
  * Assign iLO its own IP, subnet, and gateway.
  * Access remotely by opening: `https://<iLO-IP>` in a browser.
  * Default user is printed on a label attached to the server (ILO username + password).
  * From there, you can power on/off the server, open remote console, or mount ISO images.

* **Main Features**

  * Remote power control (on/off/reset).
  * Full remote console (KVM over IP).
  * Virtual media (mount ISOs/USB remotely).
  * Server health monitoring (CPU, memory, fans, power supply, storage).
  * Firmware and BIOS update remotely.
  * Event logging, alerting (email/SNMP).
  * Directory integration (LDAP/Active Directory).
  * Advanced features available with **iLO Advanced license** (like graphical remote console, multi-user collaboration, scripted management, and power capping).

* **Versions**

  * **iLO Standard** (basic management – health monitoring, remote power).
  * **iLO Advanced** (adds remote console, virtual media, advanced security & scripting).
  * **iLO Advanced Premium Security** (extra compliance/security).

* **Difference between iLO and iDRAC**

  * Both serve the same purpose (remote management of physical servers).
  * iLO = HPE’s solution, iDRAC = Dell’s solution.
  * Interfaces and naming differ, but features are largely the same.
