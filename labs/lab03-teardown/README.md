# Lab 03: Teardown without a screwdriver

**Host Model:** Acer Nitro NL16-71G  
**Environment:** Windows PowerShell  

---

## Part A: Component Identification & Upgrade Analysis

### Internal Layout Overviews
![Manual Components](evidence/manual-figures/02-manual-components.png)

### Component Breakdown
* **System Battery**
  * **What:** Rechargeable Li-ion Battery Pack (Model: HQ2403SIPSC / ~53.9Wh).
  * **Connects to:** Mainboard battery power header.
  * **Limits:** Specific physical form-factor and voltage specification; replaceable only with compatible Acer battery units.

* **Storage (M.2 NVMe SSD)**
  * **What:** M.2 2280 PCIe NVMe Solid State Drive (SCY SNN88HG51200D 512GB).
  * **Connects to:** Mainboard M.2 PCIe NVMe slot.
  * **Limits:** Subject to M.2 slot form factor (2280) and PCIe lane speed limits defined by the platform chipset.

* **System Memory (RAM)**
  * **What:** Dual-channel DDR5 SO-DIMM RAM Modules (SCY Technology + Micron Technology, 4800 MHz).
  * **Connects to:** Integrated memory controller on the CPU via SO-DIMM slots.
  * **Limits:** Max capacity and speed defined by system BIOS and processor memory controller limits.

* **Cooling Assembly & CPU**
  * **What:** Intel Core i7-13620H (10 Cores / 16 Threads) with dual cooling fans and copper heatpipes.
  * **Connects to:** BGA soldered CPU socket and system fan power headers on mainboard.
  * **Limits:** Fixed thermal dissipation limit (TDP); CPU/GPU are non-upgradeable due to BGA solder.

---

### Upgrade Verdict

| Component | Modular? | Standard Socket? | Upgradeable? | Replacement Candidate / Limits |
| :--- | :--- | :--- | :--- | :--- |
| **RAM** | Yes | Yes (SO-DIMM) | Yes | Upgradeable up to max supported DDR5 SO-DIMM capacity |
| **Storage (SSD)** | Yes | Yes (M.2 2280) | Yes | Replaceable/expandable via standard M.2 NVMe PCIe SSD |
| **CPU / GPU** | No | No (BGA Soldered) | No | Soldered directly to the mainboard; non-upgradeable |
| **Battery** | Yes | Proprietary Cable | Yes | User-replaceable with matching original model battery |

---

## Part B: System Specification Artifacts

* **Host Model Screenshot:** `evidence/01-host-model-powershell.png`
* **Raw Specs Log:** `evidence/windows-specs.txt`
* **Storage Specs:** `evidence/03-storage-disks.png`
* **Memory Specs:** `evidence/04-memory-specs.png`
* **CPU Specs:** `evidence/05-cpu-specs.png`

---

## Security Verification Notice
All unique hardware identifiers (`SerialNumber`) present in the captured logs have been sanitized and replaced with `XXXX` to prevent credential and device tracking risks.
