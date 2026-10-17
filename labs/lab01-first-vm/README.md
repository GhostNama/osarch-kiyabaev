# Lab 1 — Your first virtual machine

Pair: 
Driver first half: Anuar Kiyabaev
Machine: VMware Workstation / Ubuntu Server 26.04.1 LTS
Date: 2026-10-17

## What I did

---
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.21.129/24 metric 100 brd 192.168.21.255 scope global dynamic ens33
---
sudo systemctl status ssh
[sudo] password for anuar: 
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; disabled; preset: enabled)
     Active: active (running) since Sat 2026-10-17 09:11:58 UTC; 4min ago
---

## Result

Ubuntu Server virtual machine is successfully deployed, network interface configured with IP address `192.168.21.129`, and OpenSSH service is active and running.

## What did not work the first time

Typed `bash sudo systemctl status ssh` by mistake, which resulted in the error `/usr/bin/sudo: cannot execute binary file`. Fixed it by removing the extra `bash` prefix and executing the command directly as `sudo systemctl status ssh`.

## Evidence

- [evidence/ssh-status.png](evidence/ssh-status.png) — shows the terminal session with the network configuration (`ip a`) and the active status of the OpenSSH service.s