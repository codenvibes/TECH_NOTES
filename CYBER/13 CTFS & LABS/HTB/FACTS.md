---
tags:
  - HTB_SN_10
link: https://app.hackthebox.com/machines/Facts
description: "Easy\r·\rLinux"
image: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/bdcd209c32f156fbfb2268f099971f75.png
date: 2026-02-15
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
64 bytes from 10.129.4.179: icmp_seq=1 ttl=63 time=276 ms
64 bytes from 10.129.4.179: icmp_seq=2 ttl=63 time=279 ms
64 bytes from 10.129.4.179: icmp_seq=3 ttl=63 time=277 ms
64 bytes from 10.129.4.179: icmp_seq=4 ttl=63 time=283 ms

--- TARGET_IP ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3015ms
rtt min/avg/max/mdev = 276.453/278.758/282.843/2.585 ms
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

Command: `nmap -A -T4 -p- TARGET_IP`

```shell

```

Breakdown:
- **`nmap`**
    - **Description:** The utility itself.
    - **Purpose:** Starts the network scanning application.
- **`-A`**
    - **Description:** Aggressive Scan.
    - **Purpose:** Enables OS detection, version detection, script scanning, and traceroute. Comprehensive but noisier.
- **`-T4`**
    - **Description:** Timing Template 4 (Aggressive).
    - **Purpose:** Speeds up the scan with more aggressive timing. Faster but may be less accurate or trigger IDS/IPS.
- **`-p-`**
    - **Description:** All Ports Scan. 
    - **Purpose:** Scans all 65,535 ports. Slower but thorough.
- **`TARGET_IP`**
    - **Description:** Target Specification.
    - **Purpose:** The IP address of the host being scanned.

When you see `Did not follow redirect to http://facts.htb/` in Nmap, the server is essentially saying:

"I know you're at `TARGET_IP`, but I am configured to only talk to people who call me `facts.htb`."

This is a **301 (Permanent)** or **302 (Found)** redirect. Browsers follow this automatically, but Nmap just reports it. If the name isn't in your `/etc/hosts`, the browser follows the redirect into a "Dead End."
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

http://facts.htb/admin/login
![[Pasted image 20260215190815.png]]

![[Pasted image 20260215190524.png]]

![[Pasted image 20260215190702.png]]

![[Pasted image 20260215192125.png]]

![[Pasted image 20260215193205.png]]

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

