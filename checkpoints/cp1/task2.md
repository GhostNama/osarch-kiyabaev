# Task 2 — Plan and justify

## Allocation
- **Ubuntu Server guest:**
  - vCPUs: 1
  - Memory: 2 GB (2048 MB)
  - Disk size & type: 20 GB, dynamically allocated
  - Network adapter(s) and mode: Adapter 1 — NAT (internet access); Adapter 2 — Host-Only (direct SSH from host)
- **Windows 11 guest:**
  - vCPUs: 2
  - Memory: 4 GB (4096 MB)
  - Disk size & type: 40 GB, dynamically allocated
  - Network adapter(s) and mode: Adapter 1 — NAT (internet access)

## Arithmetic
- **Host assumptions:** The host OS needs at least 2 CPU threads and 4 GB of RAM to remain responsive (also note that `VirtualizationFirmwareEnabled` currently shows `False` in `evidence/task1.txt` and must be enabled in firmware/UEFI if hardware virtualization is required by the hypervisor).
- **CPU:** 16 logical processors available - (1 + 2) = 13 logical processors left for the host.
- **Memory:** ~15.71 GB total RAM available - (2 + 4) = ~9.71 GB left for the host (around ~0.96 GB free above current ~6.96 GB available memory).
- **Disk:** ~197.2 GB free space on C: - (20 + 40) = ~137.2 GB remaining maximum balance (dynamically allocated disks start smaller and grow as data is written).

## Justification
- **Network mode:** NAT + Host-Only is chosen instead of Bridged because campus or office networks may block extra MAC addresses or limit DHCP leases; NAT gives outbound internet via the host, while Host-Only provides a steady local IP for SSH from the host.
- **Disk type:** Dynamically allocated disks avoid reserving all 60 GB upfront on the host drive, preserving free space for the host OS.
- **Resource sizing:** Ubuntu Server has no GUI, so 1 vCPU and 2 GB RAM are sufficient; Windows 11 gets 2 vCPU and 4 GB RAM for baseline usability while leaving ample CPU, RAM, and NVMe SSD space for the host.

## Verification plan
- **On Ubuntu Server (resources & internet):**
  - Command: `nproc && free -h && ping -c 2 8.8.8.8`
  - Expected output: `1` CPU core, ~`1.9Gi` memory shown, and `0% packet loss` on ping.
- **On Windows 11 guest (resources):**
  - Command (PowerShell): `(Get-CimInstance Win32_Processor).NumberOfLogicalProcessors; [math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory / 1GB)`
  - Expected output: `2` and `4`.
- **On host (SSH to Ubuntu Server):**
  - Command: `ssh <user>@<ubuntu_host_only_ip>`
  - Expected output: Shell prompt on the Ubuntu guest (`<user>@<hostname>:~$`).
