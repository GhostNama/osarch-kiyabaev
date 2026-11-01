# Ticket 1: New Developer Test Machine Setup

## 1. The original request
Office manager Aigerim requested a test computer set up for a new developer starting on Monday, along with a guide so that the procedure could be repeated independently.

## 2. The real request was identified
The initial brief looked straightforward, but it completely omitted critical technical details:
* **Host Hardware Limitations:** The request contained no information about the host machine's available RAM and disk space. Allocating resources blindly creates a high risk of memory overcommit, forcing the host into disk swapping and causing a catastrophic drop in performance.
* **OS Versions:** Specific Linux distributions and Windows editions were not defined.
* **Network Requirements:** Network isolation, internet access, and inter-VM communication parameters were entirely missing.

**Assumptions and Trade-offs:**
* **Resource Allocation:** I decided to allocate a conservative amount of resources—4 GB of RAM and 30 GB of disk space per virtual machine (both Linux and Windows). This ensures the host system stays safely below its hardware limits, avoiding swap degradation.
* **Networking:** I chose the **NAT** network mode instead of a Bridged adapter. The local academic network filters traffic by MAC addresses, which breaks bridged connections; NAT routes traffic securely through the host IP and avoids blockages.

## 3. What was done
1. Installed and configured a hypervisor (VirtualBox / VMware Workstation) on the host machine.
2. Created the first virtual machine for Ubuntu Server, configured 4 GB RAM, 2 GB vCPU, and a 30 GB virtual hard disk.
3. Created the second virtual machine for Windows 11 with similar conservative resource limits.
4. Configured network adapters to NAT mode and established internal connectivity between the guests.
5. Encountered a firewall issue where Windows blocked ICMP echo requests (ping); resolved this by adjusting the Windows Defender firewall rules.
6. Prepared a step-by-step guide for beginners in `BUILD.md` and captured all required proofs.

## 4. Evidence
The `evidence/` directory contains the following proofs:
* `01-new-vm-wizard.png`: Shows the initial VM creation wizard configuration for the Linux guest.
* `02-disk-settings.png`: Confirms the conservative 30 GB virtual disk allocation size.
* `03-network-adapter.png`: Demonstrates the NAT network adapter selection to bypass local MAC filtering.
* `04-guest-uname.txt`: Contains the text output of the `uname -a` command proving successful Linux kernel boot.
* `05-ping-between-guests.txt`: Text output verifying stable network packet exchange between the Linux and Windows guests.
* `06-snapshot-list.png`: Displays the initial clean state snapshot taken right after the OS installations.

## 5. What I would do differently
Next time, instead of performing the manual installation steps entirely through the GUI, I would script the initial VM provisioning or use configuration templates (such as Vagrant files) to speed up the process and minimize human error during repetitive setup tasks.