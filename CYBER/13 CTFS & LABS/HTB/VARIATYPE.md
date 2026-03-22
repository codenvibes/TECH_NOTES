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
</div>

Browse to `http://variatype.htb/tools/variable-font-generator`

![[Pasted image 20260322131510.png]]
<div align="center">
<br>
<br>
</div>

#### 2.2.3 Subdomain Enumeration

The search for subdomains was conducted by manipulating the HTTP `Host` header. This technique is essential for discovering web content that is logically separated on the same physical server but assigned to different hostnames.
<div align="center">
<br>
</div>

##### Phase 1: The False Negative (Syntax Error)

The initial attempt resulted in zero hits across the entire wordlist.

**Command:** `ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://variatype.htb/ -H "Host: FUZZ.variatype.htb/"`

**Breakdown:**

- `-w`
    - **Description:** Wordlist Path.
    - **Purpose:** Specifies the list of common subdomain names to be tested against the target.
- `-u`
    - **Description:** Target URL.
    - **Purpose:** Defines the base address of the web server receiving the fuzzing requests.
- `-H "Host: FUZZ.variatype.htb/"`
    - **Description:** Custom HTTP Header.
    - **Purpose:** Injects wordlist entries into the Host header.
- `FUZZ`
	- **Description:** Keyword Placeholder.
	- **Purpose:** Acts as the injection point where `ffuf` inserts each individual entry from the selected wordlist.

**Output:**

```shell
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://variatype.htb/ -H "Host: FUZZ.variatype.htb/"

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://variatype.htb/
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.variatype.htb/
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

:: Progress: [4989/4989] :: Job [1/1] :: 72 req/sec :: Duration: [0:01:17] :: Errors: 0 ::
```

**Analysis:**

This attempt failed because HTTP `Host` headers must strictly match the domain name defined in the web server's configuration (e.g., Nginx `server_name` blocks). A trailing slash is syntactically invalid for a hostname, causing the server to ignore the header and return no valid results.
<div align="center">
<br>
<br>
</div>

##### Phase 2: The False Positive (Unfiltered Noise)

**Command:** `ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://variatype.htb/ -H "Host: FUZZ.variatype.htb"`

**Output:**

```shell
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://variatype.htb/ -H "Host: FUZZ.variatype.htb" 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://variatype.htb/
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.variatype.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

ns4                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 254ms]
mail                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 253ms]
www                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 255ms]
ns                      [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 254ms]
whm                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 255ms]
ns1                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 255ms]
m                       [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 256ms]
smtp                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 254ms]
webmail                 [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 256ms]
ns2                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 256ms]
localhost               [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 258ms]
www2                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 259ms]
autoconfig              [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 263ms]
mobile                  [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 260ms]
webdisk                 [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 261ms]
dev                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 261ms]
ns3                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 266ms]
cp                      [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 273ms]
forum                   [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 267ms]
beta                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 269ms]
cpanel                  [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 269ms]
mail2                   [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 269ms]
blog                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 269ms]
test                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 279ms]
vpn                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 272ms]
demo                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 272ms]
admin                   [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 272ms]
secure                  [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 275ms]
imap                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 275ms]
mysql                   [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 268ms]
pop3                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 268ms]
support                 [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 268ms]
ftp                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 279ms]
shop                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 270ms]
old                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 282ms]
pop                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 283ms]
mx                      [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 280ms]
new                     [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 279ms]
dns2                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 284ms]
autodiscover            [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 283ms]
dns1                    [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 263ms]
lists                   [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 265ms]

[WARN] Caught keyboard interrupt (Ctrl-C)
```

This command corrects the previous syntax error. This version revealed that the server utilizes a "Catch-All" configuration, returning a 169-byte response for every request.
<div align="center">
<br>
<br>
</div>

##### Phase 3: Discovery

To isolate real subdomains from the default server responses, a size filter was applied to suppress the 169-byte noise.

**Command:** `ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://variatype.htb/ -H "Host: FUZZ.variatype.htb" -fs 169` 

**Breakdown:**

 - `-fs 169`
	- **Description:** Filter HTTP Response Size & Targeted Filter Value.
	- **Purpose:** Instructs the tool to ignore any response that is exactly 169 bytes.


**Purpose:** To hide the "Catch-All" redirects and only display responses that differ from the server's default behavior. **Result:** This calibration successfully revealed a unique virtual host: **`portal.variatype.htb`** (Status: 200, Size: 2494).

**Output:**

``` shell
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://variatype.htb/ -H "Host: FUZZ.variatype.htb" -fs 169

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://variatype.htb/
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.variatype.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 169
________________________________________________

portal                  [Status: 200, Size: 2494, Words: 445, Lines: 59, Duration: 254ms]
:: Progress: [4989/4989] :: Job [1/1] :: 105 req/sec :: Duration: [0:00:34] :: Errors: 0 ::
```
<div align="center">
<br>
<br>
</div>

#### 2.2.4 Update Hosts File

**Update Local DNS:** Add `portal.variatype.htb` to your `/etc/hosts` file so your browser can resolve the newly discovered virtual host.

Command: `sudo sh -c 'echo "TARGET_IP portal.variatype.htb" >> /etc/hosts'`
<div align="center">
<br>
<br>
</div>

#### 2.2.5 Navigate to `http://portal.variatype.htb`

![[Pasted image 20260322172725.png]]
<div align="center">
<br>
<br>
</div>

#### 2.2.6 `http://portal.variatype.htb` Enumeration

```shell
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/dirb/common.txt -u http://portal.variatype.htb/FUZZ

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://portal.variatype.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

                        [Status: 200, Size: 2494, Words: 445, Lines: 59, Duration: 276ms]
.git/HEAD               [Status: 200, Size: 23, Words: 2, Lines: 2, Duration: 289ms]
files                   [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 253ms]
index.php               [Status: 200, Size: 2494, Words: 445, Lines: 59, Duration: 256ms]
:: Progress: [4614/4614] :: Job [1/1] :: 153 req/sec :: Duration: [0:00:31] :: Errors: 0 ::
```
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

