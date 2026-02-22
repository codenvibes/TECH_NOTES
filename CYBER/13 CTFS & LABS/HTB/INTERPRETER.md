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
64 bytes from TARGET_IP: icmp_seq=1 ttl=63 time=190 ms
64 bytes from TARGET_IP: icmp_seq=2 ttl=63 time=190 ms
64 bytes from TARGET_IP: icmp_seq=3 ttl=63 time=191 ms
64 bytes from TARGET_IP: icmp_seq=4 ttl=63 time=194 ms

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

#### 2.1.1. The "Spearfishing" Scan (All Ports, High Speed)

Command: `nmap -p- --min-rate 5000 -Pn TARGET_IP`

Breakdown:
- **`nmap`**
    - **Description:** The utility itself.
- **`-p-`**
    - **Description:** All Ports Scan. 
    - **Purpose:** Scans all 65,535 ports. Slower but thorough.
- `--min-rate 5000`
	- **Description:** Minimum Packet Rate.
	- **Purpose:** Forces Nmap to send at least 5,000 packets per second. This drastically reduces scan time on stable networks like the HTB VPN.
- `-Pn`
    - **Description:** Skip Host Discovery.
    - **Purpose:** Treats the host as "online" even if it doesn't respond to pings (ICMP). Many HTB boxes have firewalls that block pings.
- **`TARGET_IP`**
    - **Description:** Target Specification.
    - **Purpose:** The IP address of the host being scanned.

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ nmap -p- --min-rate 5000 -Pn TARGET_IP       
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-22 01:57 -0500
Warning: TARGET_IP giving up on port because retransmission cap hit (10).
Nmap scan report for TARGET_IP
Host is up (0.32s latency).
Not shown: 64926 closed tcp ports (reset), 605 filtered tcp ports (no-response)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
443/tcp  open  https
6661/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 46.08 s
```
<div align="center">
<br>
<br>
</div>

#### 2.1.2. The "Deep Dive" Scan (Targeted Aggression)

Command: `nmap -A -p 22,80,443,6661 TARGET_IP  `

Breakdown:
- `-sC`
    - **Description:** Default Script Scan.
    - **Purpose:** Runs a collection built-in Nmap Scripting Engine (NSE) scripts to find common vulnerabilities, metadata, or hidden info.
- `-sV`
    - **Description:** Version Detection.
    - **Purpose:** Probes open ports to determine what software and version are actually running (e.g., identifying "Jetty" or "OpenSSH 9.2").
- `-p 22,80,443,6661`
    - **Description:** Targeted Port List.
    - **Purpose:** Restricts the heavy scanning to only the ports you confirmed are open, saving significant time and processing power.


Output:

```shell
┌──(kali㉿kali)-[~]
└─$ nmap -A -p 22,80,443,6661 TARGET_IP         
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-22 02:04 -0500
Nmap scan report for TARGET_IP
Host is up (0.19s latency).

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 07:eb:d1:b1:61:9a:6f:38:08:e0:1e:3e:5b:61:03:b9 (ECDSA)
|_  256 fc:d5:7a:ca:8c:4f:c1:bd:c7:2f:3a:ef:e1:5e:99:0f (ED25519)
80/tcp   open  http     Jetty
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Mirth Connect Administrator
443/tcp  open  ssl/http Jetty
| ssl-cert: Subject: commonName=mirth-connect
| Not valid before: 2025-09-19T12:50:05
|_Not valid after:  2075-09-19T12:50:05
|_http-title: Mirth Connect Administrator
| http-methods: 
|_  Potentially risky methods: TRACE
|_ssl-date: TLS randomness does not represent time
6661/tcp open  unknown
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, Linux 5.0 - 5.14
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   194.09 ms 10.10.14.1
2   194.91 ms TARGET_IP

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 197.90 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.3. Scan Results Analysis

| **Service**       | **Version**             | **Analysis**                                                                                                                                                                                    |
| ----------------- | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **OpenSSH**       | `9.2p1`                 | **Low Priority.** This is a modern, patched version of SSH on Debian. Direct exploits are extremely rare. I will keep this as a potential "lateral movement" point if I find credentials later. |
| **Jetty**         | `(Web Server)`          | **Supporting Tech.** Jetty itself is usually secure, but it’s the "engine" for the real target (Mirth).                                                                                         |
| **Mirth Connect** | `Unknown` (* To Verify) | **HIGH PRIORITY.** This software has several high-severity RCE bugs (like CVE-2023-43208). <br>**Priority 1:** Find the specific version number.                                                |
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

### 2.2. Enumeration of Web Services (Port 80)
#### 2.2.1. Web Enumeration

Browse to `TARGET_IP`.
<div align="center"><br><img width="" src="Pasted image 20260222104946.png" alt=""></div>

The version isn't shown here.
<div align="center">
<br>
<br>
</div>

#### 2.2.2. Fingerprinting (The "What")

My Nmap scan and browsing `TARGET_IP` page found the page title "Mirth Connect Administrator." 

A quick Google search for "*what is mirth connect and what is it's architecture?*" revels:

> Mirth Connect is ==an open-source, cross-platform healthcare integration engine designed to facilitate bi-directional, real-time data exchange (HL7, FHIR, DICOM, X12) between disparate systems like EHRs and labs==. It acts as a central hub using a flexible channel-based architecture for filtering, transforming, and routing healthcare messages.[^1]
<div align="center">
<br>
<br>
</div>

#### 2.2.3. Fuzzing the Root (HTTP/HTTPS)

Command: `ffuf -u https://TARGET_IP/FUZZ -w /usr/share/wordlists/dirb/common.txt`

Breakdown: 

- `ffuf`
    - **Description:** Fuzz Faster U Fool.
    - **Purpose:** A web fuzzer used for directory and parameter discovery.
- `-u`
    - **Description:** Target URL. 
    - **Purpose:** Defines the base address. The keyword `FUZZ` tells the tool exactly where to insert the words from your list.
- `-w`
    - **Description:** Wordlist.
    - **Purpose:** Points to the dictionary of common folder names (e.g., `admin`, `api`, `backup`).

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ ffuf -u https://TARGET_IP/FUZZ -w /usr/share/wordlists/dirb/common.txt 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://TARGET_IP/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

                        [Status: 200, Size: 2532, Words: 174, Lines: 82, Duration: 196ms]
api                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 190ms]
css                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 188ms]
images                  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 187ms]
index.html              [Status: 200, Size: 2532, Words: 174, Lines: 82, Duration: 191ms]
js                      [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 188ms]
webadmin                [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 194ms]
:: Progress: [4614/4614] :: Job [1/1] :: 206 req/sec :: Duration: [0:00:22] :: Errors: 0 ::
```

Most modern applications use a standard structure for their APIs. Even without documentation, a pentester's first instinct is to check for common "Base Paths":

- `/api`
- `/v1`
- `/rest`

My `ffuf` scan confirmed `/api` exists and this might be the "Base Path."

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

[^1]: [Mirth Connect Explained: Architecture & HL7 Standards](https://intuitionlabs.ai/articles/mirth-connect-architecture-integration-engine-guide)
