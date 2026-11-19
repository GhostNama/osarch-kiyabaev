# Task 1 — Inventory and interpret

## 1a. The computer in front of you

| Figure | This computer | Command it came from |
| :--- | :--- | :--- |
| CPU model | 13th Gen Intel(R) Core(TM) i7-13620H[cite: 10] | `Get-CimInstance Win32_Processor \| Select-Object Name, NumberOfCores, NumberOfLogicalProcessors, VirtualizationFirmwareEnabled`[cite: 10] |
| Cores / logical processors | 10 cores / 16 logical processors[cite: 10] | `Get-CimInstance Win32_Processor \| Select-Object Name, NumberOfCores, NumberOfLogicalProcessors, VirtualizationFirmwareEnabled`[cite: 10] |
| Hardware virtualization: supported? enabled in firmware? | Supported (Intel VT-x on i7-13620H); enabled in firmware: False[cite: 10] | `Get-CimInstance Win32_Processor \| Select-Object Name, NumberOfCores, NumberOfLogicalProcessors, VirtualizationFirmwareEnabled`[cite: 10] |
| Memory: total / available now | ~15.71 GB total (16,473,976 KB) / ~6.96 GB free (7,296,404 KB)[cite: 10] | `Get-CimInstance Win32_OperatingSystem \| Select-Object Caption, Version, BuildNumber, TotalVisibleMemorySize, FreePhysicalMemory`[cite: 10] |
| System disk: size, free space, SSD or HDD | ~476–477 GB total (511,033,995,264 bytes on C: / 512,110,190,592 bytes physical), ~197.2 GB free (211,766,734,848 bytes), NVMe SSD[cite: 10] | `Get-PhysicalDisk \| Select-Object DeviceId, MediaType, BusType, Size` and `Get-Volume -DriveLetter C \| Select-Object DriveLetter, FileSystemLabel, Size, SizeRemaining`[cite: 10] |
| Partition style of the system disk | GPT[cite: 10] | `Get-Disk \| Select-Object Number, PartitionStyle, ProvisioningType`[cite: 10] |
| OS and version | Windows 11 Home (Майкрософт Windows 11 Домашняя), Version 10.0.26300 (Build 26300)[cite: 10] | `Get-CimInstance Win32_OperatingSystem \| Select-Object Caption, Version, BuildNumber, TotalVisibleMemorySize, FreePhysicalMemory`[cite: 10] |

## 1b. A machine you have never seen: a thin-and-light laptop

### 1. Both guests?
- **Answer:** No, this laptop cannot run both guests from Labs 1–2 at the same time[cite: 3].
- **Host assumptions:** The host OS needs at least 2 CPU threads and 4 GB of RAM to stay responsive[cite: 3].
- **Arithmetic:**
  - **CPU:** 1 vCPU (Ubuntu) + 2 vCPU (Windows 11) + 2 threads (host) = 5 threads needed vs 4 logical processors available (2 cores / 4 threads at 1.1 GHz)[cite: 3].
  - **Memory:** 2 GB (Ubuntu) + 4 GB (Windows 11) + 4 GB (host) = 10 GB needed vs 8 GB LPDDR4X available[cite: 3].
  - **Disk:** 20 GB (Ubuntu) + 40 GB (Windows 11) = 60 GB needed vs 31 GB free on the 128 GB eMMC[cite: 3].

### 2. Upgrade first
- **What to change/upgrade first:** Free disk space (31 GB is far too tight for two VM disks) and memory (8 GB)[cite: 3].
- **Can it be upgraded?** Nothing can be upgraded ("nothing can be") because both the 8 GB LPDDR4X RAM and the 128 GB eMMC storage are soldered to the board, and Windows 11 Home in S mode restricts installing hypervisors/apps[cite: 3].
- **What to do instead:** Switch out of S mode, store VM images on an external fast USB drive, and run only one lightweight guest (such as Ubuntu Server) at a time instead of both[cite: 3].
