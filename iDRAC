* **What iDRAC is**
  * iDRAC is Dell’s out-of-band management solution.
  * It’s a dedicated hardware chip/module embedded in Dell PowerEdge servers.
  * It allows administrators to manage, monitor, update, and troubleshoot servers remotely — even if the OS is down or the server is powered off.

* **How it works**
  * iDRAC has its own processor, memory, and network interface (separate from the main server CPU and OS).
  * It runs independently of the installed operating system.
  * You access it through a dedicated IP address (you configure this in BIOS/iDRAC settings).
  * You connect using a web browser (GUI), SSH, RACADM (CLI tool), or through APIs.
  * iDRAC provides **KVM (Keyboard, Video, Mouse) over IP**, virtual media mounting (ISO/CD remotely), power control (on/off/reset), hardware monitoring (fans, temperature, disk, memory, etc.), and firmware updates.

* **Where it is located on the server**
  * iDRAC is a **dedicated management controller chip** embedded on the motherboard of Dell PowerEdge servers.
  * Some servers provide a **dedicated RJ-45 Ethernet port** on the back of the server for iDRAC.
  * Others allow **shared mode**, where iDRAC uses the existing LAN port.
  * Inside the system board, it’s connected to sensors, power management, and video output so it can give complete control remotely.

* **How you interact with it**
  * On boot, you can press **Ctrl+E** (or F10 depending on version) to enter iDRAC setup.
  * Assign an IP, subnet mask, and gateway.
  * From your admin PC, open a browser → `https://<iDRAC-IP>` → login with credentials (default: `root / calvin` on older versions, now it forces you to set a password).
  * From there, you can manage everything remotely, including powering on/off the server.

* **Main Features**
  * Remote power on/off/reset.
  * BIOS and firmware updates without OS.
  * Remote console (KVM over IP).
  * Virtual media (mount ISOs remotely).
  * Hardware health monitoring (CPU, RAM, HDD, PSU, fans, temps).
  * System event logs and alerts (SNMP, email, syslog).
  * Role-based access and integration with Active Directory/LDAP.

* **Versions**
  * iDRAC Express (basic features).
  * iDRAC Enterprise (advanced features like virtual console & media).
  * iDRAC Datacenter (extra monitoring, scaling, integration with OpenManage Enterprise).

