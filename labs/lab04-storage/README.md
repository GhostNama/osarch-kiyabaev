# Lab 4 - Storage

## Windows

Disk 0: partition style GPT, firmware_type UEFI - because UEFI firmware can
only boot from a GPT disk, so the installer created the system disk as GPT.
New disk (Disk 1): partition style GPT - because it matches the system disk
and avoids the MBR limits (disks over 2 TB, more than four primary
partitions). A 2 GB disk would also fit MBR, so size did not force the choice.

```
PS> Get-Disk
Number Friendly Name              Serial Number  HealthStatus OperationalStatus Total Size Partition Style
0      VMware Virtual NVMe Disk   XXXX           Healthy      Online            64 GB      GPT
1      VMware Virtual NVMe Disk   XXXX           Healthy      Online            2 GB       GPT

PS> Get-Partition -DiskNumber 1
PartitionNumber DriveLetter Offset   Size     Type
1                           17408    15.98 MB Reserved
2               E           16777216 1.98 GB  Basic
```

The new disk was formatted NTFS with label LAB4 and drive letter E.

## Linux layout - before

The three new disks are the ones that are 2G, have no partitions and no
mount point: `sdb`, `sdc`, `sdd`. The system disk `sda` (25G, mounted on
`/boot` and `/`) was not touched.

```
NAME                      SIZE TYPE FSTYPE      MOUNTPOINT
loop0                    66.8M loop squashfs    /snap/core24/1643
loop1                     1.5M loop squashfs    /snap/hwctl/123
loop2                     1.5M loop squashfs    /snap/hwctl/131
loop3                    50.1M loop squashfs    /snap/snapd/27710
sda                        25G disk
|-sda1                      1M part
|-sda2                      2G part ext4        /boot
`-sda3                     23G part LVM2_member
  `-ubuntu--vg-ubuntu--lv 11.5G lvm  ext4       /
sdb                         2G disk
sdc                         2G disk
sdd                         2G disk
sr0                       2.7G rom  iso9660
```

## Linux layout - after the rebuild

```
NAME                      SIZE TYPE  FSTYPE            MOUNTPOINT
loop0                    66.8M loop  squashfs          /snap/core24/1643
loop1                     1.5M loop  squashfs          /snap/hwctl/123
loop2                     1.5M loop  squashfs          /snap/hwctl/131
loop3                    50.1M loop  squashfs          /snap/snapd/27710
sda                        25G disk
|-sda1                      1M part
|-sda2                      2G part  ext4              /boot
`-sda3                     23G part  LVM2_member
  `-ubuntu--vg-ubuntu--lv 11.5G lvm   ext4             /
sdb                         2G disk
`-sdb1                      2G part  linux_raid_member
  `-md0                     2G raid1 ext4              /mnt/mirror
sdc                         2G disk
`-sdc1                      2G part  linux_raid_member
sdd                         2G disk
`-sdd1                      2G part  linux_raid_member
  `-md0                     2G raid1 ext4              /mnt/mirror
sr0                       2.7G rom   iso9660
```

`md0` now sits on both `sdb1` and `sdd1`. `sdc1` is left with a RAID
partition that belongs to no array.

| Role            | Device |
|-----------------|--------|
| Mirror half A   | sdb1   |
| Failed half     | sdc1   |
| Replacement     | sdd1   |

## The question

Your colleague deleted the wrong file from the mirror an hour ago. Does RAID 1 help? Why?

No, RAID 1 does not help. A mirror writes every change to both disks at the same moment, and a deletion is a change like any other, so the file disappeared from both halves at once. What would have helped is a backup or a snapshot taken before the deletion, because it is a separate copy that keeps the old state.

## Evidence

All files are text and each was saved at the moment that state existed.

| File | State | Key lines |
|------|-------|-----------|
| `lsblk-before.txt` | before the mirror | `sdb`, `sdc`, `sdd` empty, 2G |
| `array-healthy.txt` | mirror built | `[2/2] [UU]`, `State : clean`, two `active sync` |
| `checksum-before.txt` | data written | SHA-256 of `proof.txt` and `random.bin` |
| `array-degraded.txt` | `sdc1` failed | `sdc1[1](F)`, `[2/1] [U_]`, `clean, degraded` |
| `checksum-after-failure.txt` | after `umount` + `mount` | identical to the "before" file, `diff` printed `MATCH` |
| `array-rebuilt.txt` | after rebuild onto `sdd1` | `[2/2] [UU]`, `State : clean`, `sdb1` + `sdd1` `active sync` |
