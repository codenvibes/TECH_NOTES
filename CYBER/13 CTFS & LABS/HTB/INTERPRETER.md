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

#### Phase 1: The "Spearfishing" Scan (All Ports, High Speed)

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

#### Phase 2: The "Deep Dive" Scan (Targeted Aggression)

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
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
### 2.2. Enumeration of Web Services (Port 80)

#### Update Hosts File

Command: `sudo sh -c 'echo "TARGET_IP facts.htb" >> /etc/hosts'`
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
#### Web Enumeration

Browse to `http://facts.htb`.

![[Pasted image 20260215184146.png]]
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
#### Directory Fuzzing

Command: `ffuf -w /usr/share/wordlists/dirb/common.txt -u http://facts.htb/FUZZ`

Output:

```shell
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/dirb/common.txt -u http://facts.htb/FUZZ

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://facts.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.hta                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 403ms]
.listing                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 959ms]
.rhosts                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 969ms]
.cache                  [Status: 200, Size: 11116, Words: 1328, Lines: 125, Duration: 969ms]
.web                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 969ms]
.bashrc                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 967ms]
                        [Status: 200, Size: 11098, Words: 1328, Lines: 125, Duration: 1021ms]
.bash_history           [Status: 200, Size: 11137, Words: 1328, Lines: 125, Duration: 1179ms]
.sh_history             [Status: 200, Size: 11131, Words: 1328, Lines: 125, Duration: 1354ms]
.forward                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 1362ms]
.svn                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1700ms]
.htpasswd               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 1717ms]
.subversion             [Status: 200, Size: 11131, Words: 1328, Lines: 125, Duration: 1838ms]
.htaccess               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 1840ms]
.swf                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 4226ms]
.ssh                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 4228ms]
.mysql_history          [Status: 200, Size: 11140, Words: 1328, Lines: 125, Duration: 4230ms]
.listings               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 4231ms]
.cvs                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 4233ms]
.config                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 4233ms]
.history                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 4239ms]
.profile                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 4252ms]
.cvsignore              [Status: 200, Size: 11128, Words: 1328, Lines: 125, Duration: 4245ms]
.perf                   [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 4255ms]
.passwd                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 4284ms]
400                     [Status: 200, Size: 6685, Words: 993, Lines: 115, Duration: 1886ms]
404                     [Status: 200, Size: 4836, Words: 832, Lines: 115, Duration: 1787ms]
500                     [Status: 200, Size: 7918, Words: 1035, Lines: 115, Duration: 1793ms]
admin                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2236ms]
admin.cgi               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2235ms]
admin.php               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2136ms]
admin.pl                [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2145ms]
ajax                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1864ms]
cache                   [Status: 200, Size: 11116, Words: 1328, Lines: 125, Duration: 2638ms]
captcha                 [Status: 200, Size: 1283, Words: 7, Lines: 5, Duration: 3560ms]
config                  [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 2180ms]
cvs                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2449ms]
CVS                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2526ms]
en                      [Status: 200, Size: 11109, Words: 1328, Lines: 125, Duration: 2439ms]
error                   [Status: 500, Size: 7918, Words: 1035, Lines: 115, Duration: 2860ms]
forward                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2697ms]
history                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2400ms]
hta                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2467ms]
htpasswd                [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2232ms]
index                   [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 2656ms]
index.htm               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2574ms]
index.html              [Status: 200, Size: 11128, Words: 1328, Lines: 125, Duration: 2543ms]
Index                   [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 2746ms]
index.php               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2490ms]
listing                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2807ms]
listings                [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2790ms]
page                    [Status: 200, Size: 19593, Words: 3296, Lines: 282, Duration: 3362ms]
passwd                  [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 2649ms]
perf                    [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 2315ms]
post                    [Status: 200, Size: 11308, Words: 1414, Lines: 152, Duration: 3189ms]
profile                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2340ms]
robots.txt              [Status: 200, Size: 99, Words: 12, Lines: 2, Duration: 2487ms]
robots                  [Status: 200, Size: 33, Words: 2, Lines: 1, Duration: 2582ms]
rss                     [Status: 200, Size: 183, Words: 20, Lines: 9, Duration: 3547ms]
search                  [Status: 200, Size: 19187, Words: 3276, Lines: 272, Duration: 2904ms]
sitemap                 [Status: 200, Size: 3508, Words: 424, Lines: 130, Duration: 2778ms]
sitemap.xml             [Status: 200, Size: 3508, Words: 424, Lines: 130, Duration: 2534ms]
sitemap.gz              [Status: 500, Size: 7918, Words: 1035, Lines: 115, Duration: 2662ms]
ssh                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2588ms]
svn                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2605ms]
swf                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2704ms]
up                      [Status: 200, Size: 73, Words: 4, Lines: 1, Duration: 2500ms]
web                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 3058ms]
welcome                 [Status: 200, Size: 11966, Words: 1481, Lines: 130, Duration: 4066ms]
:: Progress: [4614/4614] :: Job [1/1] :: 15 req/sec :: Duration: [0:04:52] :: Errors: 0 ::
```

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

