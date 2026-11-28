# Lab 7 — CLI dojo

Pair: solo · Machine: Windows 11 guest (VMware) · Date: 2026-11-28 · Shell: cmd

Solved: **18 of 20** (not attempted: #18 and #20). Every answer was produced by typing a command; no graphical tool was used to get any answer. Time is elapsed time on my stopwatch (started at 21:38), written down at the moment each challenge was solved.

Time format is mm:ss from the start of the timed part (so 63:14 means 1 h 3 min 14 s). Setup (not scored) was the five lines from the brief. The first attempt appended to an app.log left from an earlier try (123 lines), so I deleted the file and ran the three loops again. `find /c /v "" C:\dojo\logs\app.log` then returned 41.

## Results

| # | Challenge | Command | Time |
|---|-----------|---------|------|
| 1 | Print the full path of the current directory | `cd` | 02:40 |
| 2 | Computer name and logged-in account | `hostname & whoami` | 05:16 |
| 3 | Create logs\old and backup with one command | `mkdir C:\dojo\logs\old C:\dojo\backup` | 10:06 |
| 4 | notes.txt: "dojo started" and current time | `(echo dojo started& echo %time%) > C:\dojo\notes.txt` | 13:00 |
| 5 | Full path of the ping program | `where ping` | 17:30 |
| 6 | IPv4, default gateway and DNS only | `ipconfig /all \| findstr /c:"IPv4" /c:"Default Gateway" /c:"DNS Servers"` | 21:27 |
| 7 | Save process list, print its line count | `tasklist > C:\dojo\procs.txt & find /c /v "" C:\dojo\procs.txt` | 26:18 |
| 8 | Start Notepad, find PID, end it by PID | `start notepad`, then `tasklist \| findstr /i notepad`, then `taskkill /pid 3220` | 31:34 |
| 9 | Only the ERROR lines of app.log | `findstr ERROR C:\dojo\logs\app.log` | 35:16 |
| 10 | Count of app.log lines that are not INFO | `findstr /v /b "INFO" C:\dojo\logs\app.log \| find /c /v ""` | 38:32 |
| 11 | Copy app.log to backup, show both sizes | `copy C:\dojo\logs\app.log C:\dojo\backup\`, then `dir C:\dojo\logs\app.log C:\dojo\backup\app.log` | 41:20 |
| 12 | `dir C:\nothing-here`: nothing on screen, error in errors.txt | `dir C:\nothing-here >nul 2> C:\dojo\errors.txt` | 50:37 |
| 13 | Ping gateway once, print gateway OK / DOWN | `ping -n 1 192.168.21.2 >nul && echo gateway OK \|\| echo gateway DOWN` | 54:23 |
| 14 | Lines mentioning user1, case-insensitive, numbered | `findstr /i /n "user1" C:\dojo\logs\app.log` | 58:45 |
| 15 | scores.txt from highest to lowest | `sort /r C:\dojo\scores.txt` | 63:14 |
| 16 | Processes using more than 50 MB | `tasklist /fi "memusage gt 51200"` | 67:53 |
| 17 | Process listening on TCP 135, by name | `netstat -ano \| findstr ":135 " \| findstr LISTENING`, then `tasklist /svc /fi "pid eq 548"` | 75:16 |
| 18 | hosts entry for dojo.test | not attempted | – |
| 19 | Every file under C:\dojo with path and size | `for /r C:\dojo %f in (*) do @echo %f %~zf` | 80:07 |
| 20 | robocopy mirror with exit codes | not attempted | – |

## Notes and explanations

**#14: why more than one line.** `findstr` matches a substring, so `user1` also matches `user10` to `user19`. The result is 13 lines: user1 and user10-user19 in INFO (11), plus one ERROR line and one WARN line for user1.

**#12: stdout vs stderr.** My first try, `dir C:\nothing-here 2> C:\dojo\errors.txt`, still printed the volume header on the screen, because `dir` writes that header to stdout and only the "File Not Found" error goes to stderr (stream 2). Adding `>nul` discards stdout, so the screen stays empty and errors.txt contains only the error. The "File Not Found" text from errors.txt therefore appears twice in the evidence file (once per attempt).

**#13: `&&` and `||`.** `&&` runs the next command only if ping succeeds (exit code 0), `||` only if it fails. `>nul` hides ping's own output, so only "gateway OK" is printed.

**#10: counting.** `findstr /v` prints non-matching lines, `/b` anchors the match to the start of the line, and `find /c /v ""` counts lines. Result: 11 (7 ERROR + 4 WARN).

**#17: result.** Port 135 is held by `svchost.exe` (PID 548) hosting the RpcEptMapper and RpcSs services (RPC endpoint mapper).

**#16: units.** `memusage` is in KB, so 50 MB = 51200 KB.

**#8: an extra Notepad.** During the session an extra Notepad window opened by accident, so `tasklist` showed two Notepad.exe processes (2084 and 3220). I ended the one I started by PID with `taskkill /pid 3220`; the follow-up `tasklist` returned nothing. The accidental click did not provide any answer.

**#2: evidence file.** `hostname & whoami >> file` redirects only `whoami`. I re-ran it as `(hostname & whoami) >> file`, so whoami appears twice in the evidence file. For commands that chain two programs I used parentheses from then on.

**#18 and #20.** Not attempted: the time was better spent on the other eighteen, and the pass threshold (15) was already reached.

## Evidence

- `evidence/output-all-challenges.txt`: headers `===== #NN =====` plus output and errors of each solved challenge, appended with `>>` and `2>&1`.
- `evidence/screenshots/ch01.png` ... `ch17.png`, `ch19.png`: one terminal screenshot per solved challenge.
