---
tags:
  - HTB_SN_10
link: https://app.hackthebox.com/machines/Interpreter
description: "Medium\r·\rLinux"
image: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/09ad7380d2a445fff16a31fadcd35ff6.png
date: 2026-02-22
---
## Summary

| SECTION/TASK     | FLAG |
| ---------------- | ---- |
| Submit User Flag |      |
| Submit Root Flag |      |

<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 1. Reconnaissance & Discovery

### 1.1 Connecting to the HTB VPN

First, download your personalized `.ovpn` file from Hack The Box.

Connect to the HTB VPN using the `.ovpn` configuration file. This establishes a secure tunnel that allows access to the target machine’s internal network.

Command: `sudo openvpn your_file.ovpn`

Start the Machine.
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
### 1.2. Verifying the Target is Reachable

Verify that the target machine is up and reachable by performing an ICMP ping test.

Command: `ping -c 4 TARGET_IP`

Breakdown:
- `-c 4` → sends 4 packets only (clean output, fast)

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ ping -c 4 TARGET_IP
PING TARGET_IP (TARGET_IP) 56(84) bytes of data.
64 bytes from 10.129.1.153: icmp_seq=1 ttl=63 time=190 ms
64 bytes from 10.129.1.153: icmp_seq=2 ttl=63 time=190 ms
64 bytes from 10.129.1.153: icmp_seq=3 ttl=63 time=191 ms
64 bytes from 10.129.1.153: icmp_seq=4 ttl=63 time=194 ms

--- TARGET_IP ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 189.991/191.283/193.771/1.479 ms
```

A successful response confirms that the machine is active and accessible on the HTB network, allowing us to proceed with the enumeration phase.
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 2. Enumeration

### 2.1. Port Scan with Nmap

Before we can attack a system, we need to find out what "doors" are open. Doors in this context are ports. We use a tool called **Nmap** (Network Mapper) to scan the target's IP address and see what services are running.

#### Basic Scan

Command: `nmap TARGET_IP`

Breakdown:
- **`nmap`**
    - **Description:** The utility itself.
    - **Purpose:** Starts the network scanning application.
- **`TARGET_IP`**
    - **Description:** Target Specification.
    - **Purpose:** The IP address of the host being scanned.

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ nmap TARGET_IP                  
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-22 01:30 -0500
Nmap scan report for 10.129.1.153
Host is up (0.26s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 6.10 seconds
```
<div align="center">
<br>
<br>
</div>

#### Aggressive Scan

Command: `nmap -A -T4 -p- TARGET_IP`

Breakdown:
- **`-A`**
    - **Description:** Aggressive Scan.
    - **Purpose:** Enables OS detection(`-O`), version detection(`-sV`), script scanning(Running the default suite of Nmap scripts (NSE) against found ports `-sC`), and traceroute. Comprehensive but noisier.
- **`-T4`**
    - **Description:** Timing Template 4 (Aggressive).
    - **Purpose:** Speeds up the scan with more aggressive timing. Faster but may be less accurate or trigger IDS/IPS.
- **`-p-`**
    - **Description:** All Ports Scan. 
    - **Purpose:** Scans all 65,535 ports. Slower but thorough.

Output:

```shell
```
<div align="center">
<br>
<br>
</div>

#### Comparison: Basic vs. Aggressive

| Feature              | Basic                   | Aggressive                           |
| -------------------- | ----------------------- | ------------------------------------ |
| **Port Count**       | 1,000 most common       | All 65,535                           |
| **Speed**            | Very Fast (Seconds)     | Slow (Minutes/Hours)                 |
| **OS Info**          | None                    | Detailed Guess                       |
| **Service Versions** | None (just says "open") | Exact versions (e.g., Apache 2.4.41) |
| **Scripts**          | None                    | Automatic vulnerability/info scripts |
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 3. Exploitation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 4. Post-Exploitation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 5. Conclusion & Remediation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## References

