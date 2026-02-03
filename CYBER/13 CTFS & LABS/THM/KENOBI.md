---
tags:
  - THM
link: https://tryhackme.com/room/kenobi
description: Walkthrough on exploiting a Linux machine. Enumerate Samba for shares, manipulate a vulnerable version of proftpd and escalate your privileges with path variable manipulation.
image: https://tryhackme-images.s3.amazonaws.com/room-icons/46f437a95b1de43238c290a9c416c8d4.png
---
## Summary

| SECTION/TASK                                                 | FLAG                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| Task 1. Deploy the vulnerable machine                        | 7                                              |
| Task 2. Enumerating Samba for shares                         | 3<br>log.txt<br>21<br>/var                     |
| Task 3. Gain initial access with ProFtpd                     | 1.3.5<br>4<br>d0b0f3f53b6caa532a83915e19224899 |
| Task 4. Privilege Escalation with Path Variable Manipulation | /usr/bin/menu<br>3<br>                         |

<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Task 1. Deploy the vulnerable machine

This room will cover accessing a Samba share, manipulating a vulnerable version of proftpd to gain initial access and escalate your privileges to root via an SUID binary.

> **ProFTPD** is an open-source **FTP server** for Unix/Linux systems. Think of it as the software that lets other computers upload/download files from your machine using the FTP protocol.
<div>
<br>
<br>
</div>

### Questions

#### Make sure you're connected to our network and deploy the machine
==No answer needed==

##### Stage 1: Recon (Information Gathering)

###### 1.1 Connecting to the TryHackMe VPN

Before interacting with the target machine, connect to the TryHackMe VPN using my `.ovpn` configuration file. This establishes a secure tunnel that allows access to the target machine’s internal network.

Command:

`sudo openvpn <myprofile>.ovpn`

Once the VPN initialized successfully, confirm the connection by checking for the `Initialization Sequence Completed` message in the terminal.
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
###### ## 1.2. Verifying the Target is Reachable

After connecting to the VPN, verify that the target machine is up and reachable by performing an ICMP ping test.

Command: `ping -c 4 <TARGET_IP>`

Breakdown:
- `-c 4` → sends 4 packets only (clean output, fast)

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ ping -c 4 10.64.165.146
PING 10.64.165.146 (10.64.165.146) 56(84) bytes of data.
64 bytes from 10.64.165.146: icmp_seq=1 ttl=62 time=203 ms
64 bytes from 10.64.165.146: icmp_seq=2 ttl=62 time=202 ms
64 bytes from 10.64.165.146: icmp_seq=3 ttl=62 time=204 ms
64 bytes from 10.64.165.146: icmp_seq=4 ttl=62 time=202 ms

--- 10.64.165.146 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3056ms
rtt min/avg/max/mdev = 202.132/202.809/203.644/0.571 ms
```

A successful response confirms that the machine is active and accessible on the TryHackMe network, allowing us to proceed with the enumeration phase.
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

#### Scan the machine with nmap, how many ports are open?
==7==
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Task 2. Enumerating Samba for shares
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Task 3. Gain initial access with ProFtpd
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Task 4. Privilege Escalation with Path Variable Manipulation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## References

