# Lab 03: Teardown without a screwdriver

## 1. The machine

- **Exact model:** Acer Nitro Lite 16 NL16-71G (SystemFamily: Nitro Lite)
- **How I found it:** `Get-CimInstance Win32_ComputerSystem | Format-List *` on the host (Windows, not the VM). Manufacturer = Acer, Model = Nitro NL16-71G. Screenshot: `evidence/01-host-model-powershell.png`.
- **Service manual:** Acer has no public service manual for this model. I used:
  - Teardown video of the same model: https://www.youtube.com/watch?v=TUstBFbq9ss (components shown at 3:50-6:09). Declared as a last-resort source; screenshot used as the figure.
  - Acer specification via LaptopMedia: https://laptopmedia.com/in/guides/how-to-open-acer-nitro-lite-16-nl16-71g-disassembly-and-upgrade-options/
  - The video title states NL16-71G, so it is the same model.

## 2. Components

Figure: `evidence/manual-figures/02-manual-components.png` (the numbers 1-7 on the image match the list below).

1. **Cooling fans (two)**
   - What: two blower fans (video 3:50).
   - Connects to: fan headers on the system board; share the heat pipes with the heatsink.
   - Limits: two fans cap sustained CPU/GPU speed (TDP).
2. **Heat pipes and heatsink**
   - What: copper heat pipes over CPU and GPU.
   - Connects to: CPU and GPU on the system board.
   - Limits: fixed thermal capacity.
3. **CPU and GPU (under the heatsink)**
   - What: Intel Core i7-13620H, 10 cores / 16 logical CPUs; SocketDesignation `U3E1`.
   - Connects to: system board.
   - Limits: soldered (BGA), cannot be replaced.
4. **Wi-Fi card**
   - What: M.2 card (A/E key) near the fans.
   - Connects to: small M.2 slot on the board; antenna cables.
   - Limits: replaceable, held by one screw (video 3:50-6:09); the antenna cables must be reconnected.
5. **Battery**
   - What: Li-ion pack, model HQ24U3S1P5C, 11.55 V, 53.99 Wh.
   - Connects to: connector on the board.
   - Limits: fixed size and voltage; compatible Acer pack only.
6. **M.2 SSD**
   - What: SCY SMM88HG51200D, 512 GB, M.2 2280, PCIe NVMe.
   - Connects to: M.2 slot on the board.
   - Limits: 2280 size; Acer lists one 2280 PCIe 4.0 NVMe slot (video shows a second slot, see section 3).
7. **System memory**
   - What: DDR5 4800, two devices of 8 GB (SCY SM5S530AA8EMC, Micron MTC4C10163S1SC48BA1).
   - Connects to: CPU memory controller (`Controller0-ChannelA-DIMM0`, `Controller1-ChannelA`).
   - Limits: Acer: one SO-DIMM slot plus soldered memory, max 24 GB. Video 6:09: a SO-DIMM slot holding a Crucial/Micron module, label CT8G48C40S5.

## 3. Upgrade verdict

| Component | Manual says | OS says | Verdict |
|---|---|---|---|
| Memory | Acer spec: 1 SO-DIMM + soldered, max 24 GB. Video 6:09: SO-DIMM slot with a Micron/Crucial module | 2 devices, FormFactor 12, DDR5 4800, MaxCapacity 64 GB | **Partly:** one module can be replaced, total up to 24 GB |
| Storage | Acer: 1 x M.2 2280 PCIe NVMe. Video 6:09: two M.2 slots, one holds the factory SSD, the other is empty | BusType NVMe, MediaType SSD, 512 GB | **Yes:** replace with any 2280 NVMe; second slot not confirmed by a second source |
| CPU | Part of the system board | SocketDesignation U3E1 | **No:** soldered (BGA) |

**Disagreements.** The OS reports MaxCapacity 64 GB (67108864 KB) but Acer states 24 GB; I trust Acer, because MaxCapacity comes from a firmware table that vendors fill in carelessly and every tool repeats it. "Two devices" does not mean two slots: soldered memory counts as a device too. The video shows two SO-DIMM slots and a part number that differs from the OS (the sticker in the video reads CT8G48C40S5, a Crucial retail label, while the OS reports Micron MTC4C10163S1SC48BA1, a Micron OEM part number; both are Micron 8 GB DDR5-4800 modules, so they are likely the same family under different labels, but I could not confirm it), while Acer lists one slot; the video may show a different revision, so the verdict rests on the Acer specification and the video is only an illustration.

## 4. Same facts on two operating systems

Windows is the real host. Linux is an Ubuntu VM in VMware, so its values are virtual.

| Fact | Windows (host) | Linux (VM) |
|---|---|---|
| CPU model | `Win32_Processor` Name = 13th Gen Intel(R) Core(TM) i7-13620H | `lscpu` Model name = 13th Gen Intel(R) Core(TM) i7-13620H |
| Cores / logical CPUs | `NumberOfCores` = 10 / `NumberOfLogicalProcessors` = 16 | `lscpu` CPU(s) = 2 (VM allocation) |
| Total memory | `Win32_ComputerSystem` TotalPhysicalMemory = 16869351424 | `free -h` Mem total = 7.2Gi |
| Memory type, speed | `SMBIOSMemoryType` = 34 (DDR5), `Speed` = 4800 | `dmidecode -t memory`: "No Module Installed" (virtual) |
| Disk interface | `Get-PhysicalDisk` BusType = NVMe | `lsblk` TRAN = spi (virtual) |
| Disk type | `MediaType` = SSD, 512 GB | `sda` VMware Virtual S, 25G |
| Motherboard | `Win32_BaseBoard` Manufacturer and Product: see `evidence/windows-specs.txt` | `dmidecode -t baseboard`: Intel 440BX Desktop Reference Platform (invented by VMware) |

The names differ most: `NumberOfLogicalProcessors` vs `CPU(s)`, `BusType` vs `TRAN`, `TotalPhysicalMemory` in bytes vs `free -h` in Gi. The VM copies the host's CPU name but shows only 2 CPUs, 7.2 GiB of memory and a 25 GB virtual disk, and its board and disk interface are invented by the hypervisor. Part A was answered on the host, not in the VM.

Evidence: `evidence/windows-specs.txt`, `evidence/linux-specs.txt`. Serial numbers are replaced with `XXXX`.
