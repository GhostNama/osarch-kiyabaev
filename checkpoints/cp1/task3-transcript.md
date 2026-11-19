# Task 3 — Diagnose

## Transcript

| # | Evidence file (command) | What I expected | What I found | What I concluded |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `05-systemctl-failed.txt` (`systemctl --failed`) | `nightly-report.service` listed as failed | Two units failed: `legacy-sync.service` and `nightly-report.service` | Two leads to investigate; need to rule out whichever one is unrelated to the ticket. |
| 2 | `10-systemctl-status-legacy-sync.txt` (`systemctl status legacy-sync.service`) | Check whether `legacy-sync` writes `/var/log/nightly-report.log` | `Legacy file sync (old office, no longer used)` with `ExecStart=/bin/false` and `status=1/FAILURE` | Dead end: `legacy-sync` is an obsolete unrelated unit; rule it out and inspect `nightly-report` |
| 3 | `07-systemctl-status-nightly-report.txt` (`systemctl status nightly-report.service`) | Error status explaining why `nightly-report.service` fails | `Active: failed (Result: exit-code)`, `Triggeredby: * nightly-report.timer`, `status=203/EXEC`, and `Failed to execute /usr/local/bin/nightly-report.sh: No such file or directory` | Execution fails at `execve` (`203/EXEC`), even though the timer triggers it; check unit config and script file |
| 4 | `08-systemctl-cat-nightly-report.txt` (`systemctl cat nightly-report.service nightly-report.timer`) | `ExecStart` path and timer schedule (every 2 minutes) | `nightly-report.service` has `Type=oneshot` and `ExecStart=/usr/local/bin/nightly-report.sh`; `nightly-report.timer` has `OnBootSec=1min` and `OnUnitActiveSec=2min` | Timer schedule is every 2 minutes as stated in the ticket, and service runs `/usr/local/bin/nightly-report.sh` |
| 5 | `13-ls-l-usr-local-bin.txt` *(requested command not yet checked)* (`ls -l /usr/local/bin/nightly-report.sh`) | Confirm `/usr/local/bin/nightly-report.sh` exists and has execute permissions | Checked evidence pack for script inspection files (`17-cat-A-nightly-report-sh.txt`) | Proceed to inspect `/usr/local/bin/nightly-report.sh` with non-printing characters visible |
| 6 | `17-cat-A-nightly-report-sh.txt` (`cat -A /usr/local/bin/nightly-report.sh`) | Clean `#!/bin/bash` shebang and command writing to `/var/log/nightly-report.log` | Every line ends with `^M$`, including `#!/bin/bash^M$` on line 1, followed by `{ date; df -h /; free -h; } >> /var/log/nightly-report.log^M$` | CRLF (`\r\n`) line endings in the updated script break the shebang (`/bin/bash\r`), causing `203/EXEC` before `/var/log/nightly-report.log` is written |

## Root cause
`/usr/local/bin/nightly-report.sh` was saved with Windows CRLF (`\r\n`) line endings after last week's update, turning its shebang into `#!/bin/bash\r`. Proven by `17-cat-A-nightly-report-sh.txt` (showing `^M$` on every line) and `07-systemctl-status-nightly-report.txt` (showing `status=203/EXEC` and `Failed to execute /usr/local/bin/nightly-report.sh: No such file or directory`), the kernel fails to execute the script and `/var/log/nightly-report.log` is never written.

## Fix
```bash
sudo sed -i 's/\r$//' /usr/local/bin/nightly-report.sh
sudo systemctl daemon-reload
sudo systemctl reset-failed nightly-report.service
sudo systemctl start nightly-report.service
sudo systemctl enable --now nightly-report.timer
```

## Verification
1. Verify line endings are converted to LF (no `^M`):
   - Command: `cat -A /usr/local/bin/nightly-report.sh`
   - Expected output: Lines end with `$` only (no `^M$`), with `#!/bin/bash$` on line 1.
2. Verify the service runs and writes `/var/log/nightly-report.log`:
   - Command: `systemctl status nightly-report.service && tail -n 10 /var/log/nightly-report.log`
   - Expected output: `nightly-report.service` succeeds (`status=0/SUCCESS`) and `/var/log/nightly-report.log` contains new output (`date`, `df -h /`, `free -h`).
3. Verify persistence across a reboot:
   - Command: `systemctl is-enabled nightly-report.timer` (expected: `enabled`), then run `sudo reboot` and after reboot check `systemctl status nightly-report.timer` and `tail -n 10 /var/log/nightly-report.log` after 2 minutes.
   - Expected output: `nightly-report.timer` is active and triggers every 2 minutes after reboot, appending new entries to `/var/log/nightly-report.log`.
