# Lab 6 - Live processes and memory pressure

## 1. One process, three tools

The process: **VMware Tools service**, `vmtoolsd`. It runs in both guests, so the two Windows tools look at exactly the same process (same PID), and htop looks at its Linux counterpart. Task Manager and Process Explorer were captured a few minutes apart on the same PID; live figures change between captures.

| | Task Manager (Windows) | Process Explorer (Windows) | htop / ps (Linux) |
|---|---|---|---|
| Process | `vmtoolsd.exe` | `vmtoolsd.exe` | `/usr/bin/vmtoolsd` |
| PID | 3436 | 3436 | 1188 |
| Parent | not shown | `services.exe` (856) | `systemd` (1) |
| User | SYSTEM | NT AUTHORITY\SYSTEM | root |
| Private memory | Commit size 10 636 K | Private Bytes 10 288 K | - |
| Memory in RAM | Memory (active private working set) 6 292 K | Working Set 27 000 K | RES 10 948 K (SHR 9 332 K) |
| Reserved address space | - | - | VIRT 111M (ps VSZ 114 036 K) |
| Handles / file descriptors | Handles 400 | Handles 398 | 9 open fds (`/proc/1188/fd`) |
| Threads | 14 | 14 | 4 (1188 + threads 1313, 1337, 1338) |

Evidence: `evidence/task-manager.png`, `evidence/process-explorer.png`, `evidence/htop.png`, `evidence/vmtoolsd-linux.txt` (ps, fd count and `pstree -sp 1188`: `systemd(1)───vmtoolsd(1188)─┬─{vmtoolsd}(1313) ...`).

## 2. Where the tools disagree

1. **The same memory under different names.** Task Manager "Commit size" and Process Explorer "Private Bytes" are the same quantity: memory committed to this process alone (10 636 K vs 10 288 K; the gap is the time between the two captures).
2. **"Memory" is not the working set.** Task Manager's default "Memory" column is the *private* part of the working set (6 292 K); Process Explorer's "Working Set" also counts shared pages such as DLLs used by other processes (27 000 K). The same split exists in htop: RES 10 948 K, of which SHR 9 332 K is shared.
3. **Parent.** Task Manager does not show the parent at all. Process Explorer shows `services.exe`, htop and pstree show `systemd`: in both systems the parent is the service manager.
4. **Handles vs file descriptors.** 398-400 handles on Windows against 9 fds on Linux is not a difference in work done. A Windows handle refers to any kernel object: the lower pane of Process Explorer lists ALPC ports, Desktop, Directory, Event, File and registry Key handles. A Linux fd covers open files, sockets, pipes and a few kernel objects that are opened as files (such as eventfd or epoll); registry keys and most synchronisation objects that Windows counts as handles do not exist as fds.
5. **One process or four?** `ps` and `pstree` show one process (1188) with threads in `{}`; htop by default lists each thread as its own green row with its own ID (1313, 1337, 1338), all with the same VIRT and RES because they share the process's memory.
6. **Units.** The test script counted in MB (10^6 bytes) while `free` and the kernel report MiB/GiB (2^20, 2^30): the script's 9 300 MB is about 8.7 GiB, which matches the kernel's `total-vm: 9198732kB`.

## 3. Memory pressure (Linux guest)

Guest: 7.2 GiB RAM, 2.0 GiB swap (`/swap.img`). Snapshot `before-lab6-memory` taken first. A Python loop allocated 100 MB per iteration while a second SSH session recorded `free -h` every 2 seconds. Full record: `evidence/memory-pressure.txt` (before, 124 "during" samples, after, and a summary table).

| Time (UTC) | Phase | mem used | available | swap used |
|---|---|---|---|---|
| 15:15:51 | before | 604 Mi | 6.6 Gi | 0 |
| 15:15:57 - 15:17:53 | allocation, all in RAM | rises to 6.8 Gi | falls to 414 Mi | 0 |
| 15:17:59 | swapping starts | 7.0 Gi | 251 Mi | 24 Ki |
| 15:18:06 - 15:18:38 | pressure: RAM full | stuck at 7.1 Gi | 63-131 Mi | 191 Mi -> 1.8 Gi |
| 15:18:43 | kernel OOM killer kills python3 | | | |
| 15:18:45 | after | 518 Mi | 6.7 Gi | 4.1 Mi |

- **Swap figure:** the kernel did not touch swap while free RAM was available; swap use began only when `available` fell to about 250 MiB, and then grew by roughly 50 MiB/s while `used` stayed flat at 7.1 Gi: every new page went to swap.
- **Responsiveness:** in the last ~40 seconds both SSH sessions slowed visibly: the script's counter and the `free` samples appeared more slowly than at the start, and the laptop's fans sped up. The guest's swap is a file on the host SSD, so swapping turned into disk and CPU load on the host: thrashing.
- **The process:** it was killed at 9 300 MB by the script's count, when swap was almost full (1.8 of 2.0 Gi). The shell printed `Killed`.
- **After:** memory and swap were freed within two seconds, because the swapped-out pages belonged to the killed process.

## 4. The kernel's record

`evidence/kernel-log-extract.txt` (`journalctl -k`, filtered for OOM):
- `python3 invoked oom-killer: ... order=0, oom_score_adj=0` (15:18:43)
- `oom-kill:constraint=CONSTRAINT_NONE ... global_oom ... task=python3,pid=2630,uid=1000`
- `Out of memory: Killed process 2630 (python3) total-vm:9198732kB, anon-rss:7036804kB ...`

`global_oom` with `CONSTRAINT_NONE` means the whole system ran out of memory, not a per-group limit. Of the process's 8.8 GiB of virtual memory, about 6.7 GiB was resident in RAM (anon-rss); most of the remaining ~2 GiB had been pushed to swap, which matches the 1.8 GiB of swap in use at the last sample before the kill. The kill was made by the kernel's OOM killer.

## 5. The Windows side

Windows reacts differently: it accounts for **commit** (memory promised to processes, limited by RAM + page file) and refuses an allocation instead of killing a process. A PowerShell loop committed 200 MB per step on `lab02-win11` (snapshot taken first). Record: `evidence/windows-event-extract.txt`.

- The loop stopped at **16 600 MB** with `System.OutOfMemoryException`; PowerShell itself kept running.
- With the memory still held, Committed Bytes was 21 158 621 184 of a Commit Limit of 21 286 019 072 (99.4 %), while 8 340 MB of RAM was still available (the sample saved in the evidence file; an earlier sample read 21 096 214 528 and 8 355 MB): the memory was promised, not used, because Windows gives physical pages only when they are first touched.
- The event log recorded **System, Application Popup, Event ID 26** at 9:37:18 PM (guest clock): "Windows - Virtual Memory Minimum Too Low: Your system is low on virtual memory. Windows is increasing the size of your virtual memory paging file. During this process, memory requests for some applications may be denied." That is the Windows record of the same class of event.
- After the memory was released, Committed Bytes fell to 3 712 184 320, but the Commit Limit stayed at 21 286 019 072: the page file that grew is not shrunk straight away.
- No Resource-Exhaustion-Detector event (ID 2004) appeared during the test.

| | Linux | Windows |
|---|---|---|
| What runs out | RAM + swap | commit limit (RAM + page file) |
| System response | OOM killer kills a process | allocation refused, page file grown |
| The process | killed (`Killed`) | gets an exception, keeps running |
| Log record | kernel: `Out of memory: Killed process 2630` | Application Popup, Event 26 |

Each guest's times are from its own clock (Ubuntu in UTC, Windows in its local time zone).

## What I would do differently

I did not record the Windows commit counters before starting the allocation, so the Windows "before" figure is only the after-release value. Next time I would capture before, during and after on both guests in the same way.
