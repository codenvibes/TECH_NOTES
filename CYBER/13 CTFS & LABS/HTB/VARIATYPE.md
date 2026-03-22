---
tags:
  - HTB_SN_10
link: https://app.hackthebox.com/machines/VariaType
description: Medium·Linux
image: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/1c63aff74baeaf6afdb5f35519756ab1.png
date: 2026-03-22
pawned:
---
## Summary

| SECTION/TASK     | FLAG |
| ---------------- | ---- |
| Submit User Flag |      |
| Submit Root Flag |      |

<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
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

### 1.2 Verifying the Target is Reachable

Verify that the target machine is up and reachable by performing an ICMP ping test.

Command: `ping -c 4 TARGET_IP`

Breakdown:
- `-c 4` → sends 4 packets only (clean output, fast)

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ ping -c 4 TARGET_IP
PING TARGET_IP (TARGET_IP) 56(84) bytes of data.
64 bytes from TARGET_IP: icmp_seq=1 ttl=63 time=254 ms
64 bytes from TARGET_IP: icmp_seq=2 ttl=63 time=256 ms
64 bytes from TARGET_IP: icmp_seq=3 ttl=63 time=262 ms
64 bytes from TARGET_IP: icmp_seq=4 ttl=63 time=266 ms

--- TARGET_IP ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 254.200/259.641/266.291/4.931 ms
```

A successful response confirms that the machine is active and accessible on the HTB network, allowing us to proceed with the enumeration phase.
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 2. Enumeration

### 2.1 Port Scan with Nmap

Before we can attack a system, we need to find out what "doors" are open. Doors in this context are ports. We use a tool called **Nmap** (Network Mapper) to scan the target's IP address and see what services are running.

#### 2.1.1 The "Spearfishing" Scan (All Ports, High Speed)

Command: `nmap -p- --min-rate 5000 -Pn TARGET_IP`

Breakdown:
- **`nmap`**
    - **Description:** The utility itself.
- **`-p-`**
    - **Description:** All Ports Scan. 
    - **Purpose:** Scans all 65,535 ports. Slower but thorough.
- `--min-rate 5000`
	- **Description:** Minimum Packet Rate.
	- **Purpose:** Forces Nmap to send at least 5,000 packets per second. This reduces scan time on stable networks like the HTB VPN.
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
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-22 05:14 -0400
Warning: TARGET_IP giving up on port because retransmission cap hit (10).
Nmap scan report for TARGET_IP
Host is up (0.30s latency).
Not shown: 65269 closed tcp ports (reset), 264 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 45.86 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.2 The "Deep Dive" Scan (Targeted Aggression)

Command: `nmap -A -p p1,p2,p3,p4 TARGET_IP`

Breakdown:
- **`-A`**
    - **Description:** Aggressive Scan Mode.
    - **Purpose:** Enables OS detection, version detection, script scanning (`-sC`), and traceroute all at once.
- `-p`
    - **Description:** Targeted Port List.
    - **Purpose:** Restricts the heavy scanning to only the ports you confirmed are open, saving significant time and processing power.


Output:

```shell
┌──(kali㉿kali)-[~]
└─$ nmap -A -p 22,80 TARGET_IP            
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-22 05:15 -0400
Nmap scan report for TARGET_IP
Host is up (0.26s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 e0:b2:eb:88:e3:6a:dd:4c:db:c1:38:65:46:b5:3a:1e (ECDSA)
|_  256 ee:d2:bb:81:4d:a2:8f:df:1c:50:bc:e1:0e:0a:d1:22 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-title: Did not follow redirect to http://variatype.htb/
|_http-server-header: nginx/1.22.1
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   253.97 ms 10.10.14.1
2   254.20 ms TARGET_IP

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.42 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.3 Scan Results Analysis

| Port   | **Service** | **Version**   | **Analysis**                                                                                                         |
| ------ | ----------- | ------------- | -------------------------------------------------------------------------------------------------------------------- |
| 22/tcp | SSH         | OpenSSH 9.2p1 | Running on Debian 12. Likely used for remote management; standard version with no immediate public exploits.         |
| 80/tcp | HTTP        | nginx 1.22.1  | Redirects to `http://variatype.htb/`. This indicates a Virtual Host setup is in place, requiring a local DNS update. |

<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

### 2.2 Enumeration of Web Services

#### 2.2.1 Update Hosts File

Command: `sudo sh -c 'echo "TARGET_IP variatype.htb" >> /etc/hosts'`

Breakdown:

- **`sudo`**
    - **Description:** Superuser Do.
    - **Purpose:** Executes the subsequent command with root privileges. This is required because `/etc/hosts` is a system-protected file that ordinary users cannot modify.
- **`sh -c`**
    - **Description:** Shell Command String.
    - **Purpose:** Tells the system to run a new shell instance and execute the string inside the single quotes. This is used here because the redirection (`>>`) needs root privileges to write to the file; simply using `sudo echo ... >> /etc/hosts` would fail because the redirection is handled by your current (unprivileged) shell.
- **`echo "TARGET_IP variatype.htb"`**
    - **Description:** Standard Output Generator. 
    - **Purpose:** Creates a string containing the IP address and the desired domain name, which acts as a local DNS entry.
- **`>>`**
    - **Description:** Append Redirection.
    - **Purpose:** Directs the output of the `echo` command to the end of a file. Using `>>` (append) instead of `>` (overwrite) ensures you don't accidentally delete your existing host mappings.
- **`/etc/hosts`**
    - **Description:** Static Host Lookup Table.
    - **Purpose:** The target file where the operating system looks first to resolve hostnames to IP addresses before querying external DNS servers.
<div align="center">
<br>
<br>
</div>

##### How Hostname Resolution Works

When you type a URL like `google.com` or `variatype.htb` into your browser, your computer needs to translate that text into a numerical IP address. It follows a specific order of operations:

1. **The Browser Cache:** Your browser checks if it already knows the IP from a previous visit.
2. **The Hosts File (`/etc/hosts`):** This is your computer's "private address book." It checks here **first** before asking the internet. If an entry exists, it stops looking and goes to that IP.
3. **DNS Servers:** If the name isn't in your private book, your computer asks a DNS server (like Google’s `8.8.8.8` or your ISP).

##### Why `google.com` works but `variatype.htb` doesn't

- **Public Sites:** `google.com` is registered on public DNS servers. When you ask the internet "Where is Google?", the internet has an official answer.

- **HTB Sites:** `variatype.htb` is a **private domain** inside the HTB lab environment. Public DNS servers have no idea it exists. Because your computer can't find an "official" record, it gives you a "Server Not Found" error—unless you write the address into your private address book (`/etc/hosts`) yourself.
<div align="center">
<br>
<br>
</div>

#### 2.2.2 Web Enumeration

Browse to `http://variatype.htb`.

![[Pasted image 20260322124112.png]]
<div align="center">
<br>
</div>

Browse to `http://variatype.htb/services`

![[Pasted image 20260322131231.png]]


<div align="center">
<br>
<br>
</div>

#### 2.2.3 Directory Fuzzing

**Command:** `ffuf -w /usr/share/wordlists/dirb/common.txt -u http://variatype.htb/FUZZ`

**Breakdown:**
- **`-u`**
    - **Description:** Target URL.
    - **Purpose:** Specifies the URL to be fuzzed. The keyword `FUZZ` tells the tool exactly where to inject the words from your wordlist.
- **`-w`**
    - **Description:** Wordlist Path.
    - **Purpose:** Provides the list of common directory and file names (like `admin`, `config`, etc.) to test against the server.

**Output:**

``` shell
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/dirb/common.txt -u http://variatype.htb/FUZZ 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://variatype.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

                        [Status: 200, Size: 2321, Words: 337, Lines: 60, Duration: 998ms]
services                [Status: 200, Size: 3339, Words: 548, Lines: 84, Duration: 270ms]
:: Progress: [4614/4614] :: Job [1/1] :: 121 req/sec :: Duration: [0:00:33] :: Errors: 0 ::
```
<div align="center">
<br>
<br>
</div>

#### 2.2.4 


<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 3. Exploitation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 4. Post-Exploitation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 5. Conclusion & Remediation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## References

