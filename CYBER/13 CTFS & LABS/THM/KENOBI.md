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

##### Make sure you're connected to our network and deploy the machine
==Answer==

##### Scan the machine with nmap, how many ports are open?
==Answer==
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

