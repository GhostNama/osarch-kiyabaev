# Lab 0 — Inventory your own machine

| What | Value | Where I got it |
| :--- | :--- | :--- |
| CPU Model | 13th Gen Intel(R) Core(TM) i7-13620H | `Get-CimInstance Win32_Processor` |
| Physical Cores | 10 cores | `Get-CimInstance Win32_Processor` |
| Logical Threads | 16 threads | `Get-CimInstance Win32_Processor` |
| Total Memory | 16 GB | `Get-CimInstance Win32_PhysicalMemory` |
| Module Count | 2 modules (2x8GB) | `Get-CimInstance Win32_PhysicalMemory` |
| Memory Speed | 4800 MHz | `Get-CimInstance Win32_PhysicalMemory` |
| Disk Model | NVMe SCY SMM8HG51200D 512GB SSD | `Get-PhysicalDisk` |
| Disk Type | NVMe SSD | `Get-PhysicalDisk` |
| Free Disk Space | 403.82 GB free on C: | `Get-Volume` |
| Firmware Type | UEFI | `Get-CimInstance Win32_BIOS` |
| Firmware Version & Date | Insyde F.12, 11/15/2023 | `Get-CimInstance Win32_BIOS` |
| Hardware Virtualization | True | `(Get-CimInstance Win32_Processor).VirtualizationFirmwareEnabled` |

## What did not work
Running `Get-Volume` initially produced a long list of system partitions, making it slightly unclear which volume stored the OS. Filtering by `DriveLetter C` clearly showed the 403.82 GB remaining free space on the main volume.

## Notes
Hardware virtualization (Intel VT-x / AMD-V) is enabled in BIOS and ready for setting up virtual machines.

## Evidence
- [evidence/cpu.txt](evidence/cpu.txt) — Raw CLI output verifying CPU model, 10 cores, and 16 threads.
- [evidence/memory.txt](evidence/memory.txt) — Raw CLI output verifying 2x8GB DDR5 4800MHz RAM modules.
- [evidence/disk.txt](evidence/disk.txt) — Raw CLI output verifying NVMe SSD model and 403.82 GB free space on C:.ss