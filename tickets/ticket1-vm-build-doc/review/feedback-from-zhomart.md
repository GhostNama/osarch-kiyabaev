# Feedback on anuar's BUILD.md
Reviewer: Kazakbay Zhomart
Commit reviewed: https://github.com/GhostNama/osarch-kiyabaev/tree/main/tickets
Hypervisor I used: VMware Workstation Pro, version 17

## First step where I got stuck
Step number: Step 3
What the step says: "Open the terminal on the Linux guest and check the network status. Test communication between the guests using the ping command."
What I did: Opened the terminal in Ubuntu and tried to test the network and check connectivity with Windows.
What happened / what I had to guess: The instructions do not provide the exact terminal commands to run (e.g., ip a or uname -a). Additionally, there are no instructions on how or where to save the evidence files (e.g., 04-guest-uname.txt or 05-ping-between-guests.txt), nor is the target directory specified. I had to guess the output formats, redirection commands, and target paths manually.

## Other places I hesitated
- Step 3: Mentioned adjusting "Windows Defender firewall settings", but didn't specify the exact PowerShell command or GUI navigation path to allow ICMP echo requests.
- Step 3: Recommended taking a "clean-state snapshot", but did not define the required exact name for the snapshot (e.g., Initial-Configuration).

## Settings I would not have chosen without the document
- Allocating exactly 4 GB RAM and 30 GB disk space for the Ubuntu VM, and setting the Network Adapter specifically to NAT mode to bypass academic MAC filtering.

## One thing that worked well
- Explaining the explicit technical reason for choosing NAT mode instead of Bridged mode was very helpful and informative.