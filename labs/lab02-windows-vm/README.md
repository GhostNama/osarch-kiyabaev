Here is the complete **`README.md`** file translated into English for your GitHub repository:

```markdown
# Lab Work: Virtualization, Network Connectivity, and Memory Overcommit Analysis in VMware

This repository contains the report and screenshots for the lab assignment on configuring virtual machines (Windows 11 and Ubuntu) in VMware Workstation, verifying network connectivity, and analyzing the Memory Overcommit mechanism.

---

## 📋 Content & Screenshots

All required screenshots are stored in the root directory of this repository and grouped by lab stage:

### 1. Guest OS Optimization (Windows 11)
* Disabling telemetry, diagnostic data collection, and privacy-related services:
  `![Telemetry Off](./01-win11-telemetry-off.png)`[cite: 9]
* Verifying VMware Tools installation and full-screen resolution support:
  `![VMware Tools Installed](./02-vmware-tools-installed.png)`[cite: 8]

### 2. Network Configuration & Verification
* Obtaining network credentials for the Windows 11 VM (`ipconfig` output):
  `![Windows IP Config](./03-win11-ipconfig.png)`[cite: 4]
* Configuring Windows Firewall (allowing inbound ICMP / ping requests via PowerShell):
  `![Firewall Rule](./04-win11-firewall-rule.png)`[cite: 13]
* Verifying network connectivity (successful ping from Ubuntu to Windows 11 with 0% packet loss):
  `![Ubuntu Ping Windows](./05-ubuntu-ping-win11.png)`[cite: 12]

### 3. Memory Overcommit Analysis
* VMware virtual machine hardware settings (allocating excess RAM to demonstrate overcommit):
  `![Memory Overcommit Settings](./06-vmware-memory-overcommit.png)`[cite: 3]
* RAM utilization monitoring **inside the Windows 11 VM** under workload:
  `![Windows Task Manager RAM](./07-win11-task-manager-ram.png)`[cite: 10]
* Physical RAM monitoring **on the Host system** while running VMs under load (demonstrating Overcommit in action):
  `![Host Task Manager RAM](./08-host-task-manager-ram.png)`[cite: 10]

---

## 🛠 Technical Environment
* **Hypervisor:** VMware Workstation Pro
* **Host System:** Laptop (16 GB RAM)[cite: 7]
* **Guest OS 1:** Ubuntu (`lab01-ubuntu`)[cite: 4]
* **Guest OS 2:** Windows 11 (`lab02-win11`)[cite: 4]
