# Task 3 — Diagnose

## Transcript

| # | Evidence file (command) | What I expected | What I found | What I concluded |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `05-systemctl-failed.txt` (`systemctl --failed`) | `nightly-report.service` listed as failed[cite: 6] | Two units failed: `legacy-sync.service` and `nightly-report.service`[cite: 6, 15] | Two leads to investigate; need to rule out whichever one is unrelated to the ticket[cite: 6] |
| 2 | `10-systemctl-status-legacy-sync.txt` (`systemctl status legacy-sync.service`) | Check whether `legacy-sync` writes `/var/log/nightly-report.log`[cite: 5, 15, 16] | `Legacy file sync (old office, no longer used)` with `ExecStart=/bin/false` and `status=1/FAILURE`[cite: 16] | Dead end: `legacy-sync` is an obsolete unrelated unit; rule it out and inspect `nightly-report`[cite: 7, 16] |
| 3 | `07-systemctl-status-nightly-report.txt` (`systemctl status nightly-report.service`) | Error status explaining why `nightly-report.service` fails[cite: 15, 17] | `Active: failed (Result: exit-code)`, `Triggeredby: * nightly-report.timer`, `status=203/EXEC`, and `Failed to execute /usr/local/bin/nightly-report.sh: No such file or directory`[cite: 17] | Execution fails at `execve` (`203/EXEC`), even though the timer triggers it; check unit config and script file[cite: 17] |
| 4 | `08-systemctl-cat-nightly-report.txt` (`systemctl cat nightly-report.service nightly-report.timer`) | `ExecStart` path and timer schedule (every 2 minutes)[cite: 5, 15, 18] | `nightly-report.service` has `Type=oneshot` and `ExecStart=/usr/local/bin/nightly-report.sh`; `nightly-report.timer` has `OnBootSec=1min` and `OnUnitActiveSec=2min`[cite: 18] | Timer schedule is every 2 minutes as stated in the ticket, and service runs `/usr/local/bin/nightly-report.sh`[cite: 5, 18] |
| 5 | `13-ls-l-usr-local-bin.txt` *(requested command not yet checked)* (`ls -l /usr/local/bin/nightly-report.sh`) | Confirm `/usr/local/bin/nightly-report.sh` exists and has execute permissions[cite: 15] | Checked evidence pack for script inspection files (`17-cat-A-nightly-report-sh.txt`) | Proceed to inspect `/usr/local/bin/nightly-report.sh` with non-printing characters visible[cite: 15, 19] |
| 6 | `17-cat-A-nightly-report-sh.txt` (`cat -A /usr/local/bin/nightly-report.sh`) | Clean `#!/bin/bash` shebang and command writing to `/var/log/nightly-report.log`[cite: 5, 15, 19] | Every line ends with `^M$`, including `#!/bin/bash^M$` on line 1, followed by `{ date; df -h /; free -h; } >> /var/log/nightly-report.log^M$`[cite: 19] | CRLF (`\r\n`) line endings in the updated script break the shebang (`/bin/bash\r`), causing `203/EXEC` before `/var/log/nightly-report.log` is written[cite: 5, 17, 19] |

## Root cause
`/usr/local/bin/nightly-report.sh` was saved with Windows CRLF (`\r\n`) line endings after last week's update, turning its shebang into `#!/bin/bash\r`[cite: 5, 19]. Proven by `17-cat-A-nightly-report-sh.txt` (showing `^M$` on every line) and `07-systemctl-status-nightly-report.txt` (showing `status=203/EXEC` and `Failed to execute /usr/local/bin/nightly-report.sh: No such file or directory`), the kernel fails to execute the script and `/var/log/nightly-report.log` is never written[cite: 5, 7, 17, 19].

## Fix
```bash
sudo sed -i 's/\r$//' /usr/local/bin/nightly-report.sh
sudo systemctl daemon-reload
sudo systemctl reset-failed nightly-report.service
sudo systemctl start nightly-report.service
sudo systemctl enable --now nightly-report.timer
