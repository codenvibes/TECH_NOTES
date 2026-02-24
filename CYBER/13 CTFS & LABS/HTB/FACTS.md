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
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-23 08:46 -0500
Warning: TARGET_IP giving up on port because retransmission cap hit (10).
Nmap scan report for TARGET_IP
Host is up (0.28s latency).
Not shown: 65513 closed tcp ports (reset)
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    open     http
217/tcp   filtered dbase
3615/tcp  filtered start-network
7949/tcp  filtered unknown
9950/tcp  filtered apc-9950
12724/tcp filtered unknown
13122/tcp filtered unknown
17541/tcp filtered unknown
18737/tcp filtered unknown
18903/tcp filtered unknown
34584/tcp filtered unknown
34618/tcp filtered unknown
36754/tcp filtered unknown
38375/tcp filtered unknown
42612/tcp filtered unknown
42776/tcp filtered unknown
43615/tcp filtered unknown
54321/tcp open     unknown
59307/tcp filtered unknown
61782/tcp filtered unknown
64434/tcp filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 34.82 seconds
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
┌──(kali㉿kali)-[~]
└─$ nmap -A -p 22,80,54321 TARGET_IP      
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-23 08:48 -0500
Nmap scan report for TARGET_IP
Host is up (0.26s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 9.9p1 Ubuntu 3ubuntu3.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4d:d7:b2:8c:d4:df:57:9c:a4:2f:df:c6:e3:01:29:89 (ECDSA)
|_  256 a3:ad:6b:2f:4a:bf:6f:48:ac:81:b9:45:3f:de:fb:87 (ED25519)
80/tcp    open  http    nginx 1.26.3 (Ubuntu)
|_http-server-header: nginx/1.26.3 (Ubuntu)
|_http-title: Did not follow redirect to http://facts.htb/
54321/tcp open  http    Golang net/http server
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 400 Bad Request
|     Accept-Ranges: bytes
|     Content-Length: 303
|     Content-Type: application/xml
|     Server: MinIO
|     Strict-Transport-Security: max-age=31536000; includeSubDomains
|     Vary: Origin
|     X-Amz-Id-2: dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8
|     X-Amz-Request-Id: 1896E4521768128A
|     X-Content-Type-Options: nosniff
|     X-Xss-Protection: 1; mode=block
|     Date: Mon, 23 Feb 2026 13:49:04 GMT
|     <?xml version="1.0" encoding="UTF-8"?>
|     <Error><Code>InvalidRequest</Code><Message>Invalid Request (invalid argument)</Message><Resource>/nice ports,/Trinity.txt.bak</Resource><RequestId>1896E4521768128A</RequestId><HostId>dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8</HostId></Error>
|   GenericLines, Help, RTSPRequest, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 400 Bad Request
|     Accept-Ranges: bytes
|     Content-Length: 276
|     Content-Type: application/xml
|     Server: MinIO
|     Strict-Transport-Security: max-age=31536000; includeSubDomains
|     Vary: Origin
|     X-Amz-Id-2: dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8
|     X-Amz-Request-Id: 1896E44CFEA01D99
|     X-Content-Type-Options: nosniff
|     X-Xss-Protection: 1; mode=block
|     Date: Mon, 23 Feb 2026 13:48:42 GMT
|     <?xml version="1.0" encoding="UTF-8"?>
|     <Error><Code>InvalidRequest</Code><Message>Invalid Request (invalid argument)</Message><Resource>/</Resource><RequestId>1896E44CFEA01D99</RequestId><HostId>dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8</HostId></Error>
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Vary: Origin
|     Date: Mon, 23 Feb 2026 13:48:43 GMT
|_    Content-Length: 0
|_http-server-header: MinIO
|_http-title: Did not follow redirect to http://TARGET_IP:9001
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port54321-TCP:V=7.98%I=7%D=2/23%Time=699C5ABA%P=x86_64-pc-linux-gnu%r(G
SF:enericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20
SF:text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\
SF:x20Request")%r(GetRequest,2B0,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nAc
SF:cept-Ranges:\x20bytes\r\nContent-Length:\x20276\r\nContent-Type:\x20app
SF:lication/xml\r\nServer:\x20MinIO\r\nStrict-Transport-Security:\x20max-a
SF:ge=31536000;\x20includeSubDomains\r\nVary:\x20Origin\r\nX-Amz-Id-2:\x20
SF:dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8\r\nX-A
SF:mz-Request-Id:\x201896E44CFEA01D99\r\nX-Content-Type-Options:\x20nosnif
SF:f\r\nX-Xss-Protection:\x201;\x20mode=block\r\nDate:\x20Mon,\x2023\x20Fe
SF:b\x202026\x2013:48:42\x20GMT\r\n\r\n<\?xml\x20version=\"1\.0\"\x20encod
SF:ing=\"UTF-8\"\?>\n<Error><Code>InvalidRequest</Code><Message>Invalid\x2
SF:0Request\x20\(invalid\x20argument\)</Message><Resource>/</Resource><Req
SF:uestId>1896E44CFEA01D99</RequestId><HostId>dd9025bab4ad464b049177c95eb6
SF:ebf374d3b3fd1af9251148b658df7ac2e3e8</HostId></Error>")%r(HTTPOptions,5
SF:9,"HTTP/1\.0\x20200\x20OK\r\nVary:\x20Origin\r\nDate:\x20Mon,\x2023\x20
SF:Feb\x202026\x2013:48:43\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r(RTSP
SF:Request,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text
SF:/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20R
SF:equest")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:
SF:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20
SF:Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1\x20400\x20Bad\x20Request
SF:\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20clo
SF:se\r\n\r\n400\x20Bad\x20Request")%r(FourOhFourRequest,2CB,"HTTP/1\.0\x2
SF:0400\x20Bad\x20Request\r\nAccept-Ranges:\x20bytes\r\nContent-Length:\x2
SF:0303\r\nContent-Type:\x20application/xml\r\nServer:\x20MinIO\r\nStrict-
SF:Transport-Security:\x20max-age=31536000;\x20includeSubDomains\r\nVary:\
SF:x20Origin\r\nX-Amz-Id-2:\x20dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af
SF:9251148b658df7ac2e3e8\r\nX-Amz-Request-Id:\x201896E4521768128A\r\nX-Con
SF:tent-Type-Options:\x20nosniff\r\nX-Xss-Protection:\x201;\x20mode=block\
SF:r\nDate:\x20Mon,\x2023\x20Feb\x202026\x2013:49:04\x20GMT\r\n\r\n<\?xml\
SF:x20version=\"1\.0\"\x20encoding=\"UTF-8\"\?>\n<Error><Code>InvalidReque
SF:st</Code><Message>Invalid\x20Request\x20\(invalid\x20argument\)</Messag
SF:e><Resource>/nice\x20ports,/Trinity\.txt\.bak</Resource><RequestId>1896
SF:E4521768128A</RequestId><HostId>dd9025bab4ad464b049177c95eb6ebf374d3b3f
SF:d1af9251148b658df7ac2e3e8</HostId></Error>");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   261.62 ms 10.10.14.1
2   261.63 ms TARGET_IP

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 56.28 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.3. Scan Results Analysis

| **Service**       | **Version**             | **Analysis**                                                                                                                                                                                                        |
| ----------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SSH** (22)      | OpenSSH 9.9p1           | Low Priority. Standard SSH service running on **Ubuntu Linux**. The version is quite recent, suggesting the OS is likely Ubuntu 24.04 or similar. No immediate low-hanging fruit here.                              |
| **HTTP** (80)     | nginx 1.26.3            | Web server that forces a redirect to `http://facts.htb/`. This confirms we need to perform **Virtual Host mapping** in our `/etc/hosts` file to access the site content.                                            |
| **MinIO** (54321) | Golang net/http (MinIO) | An object storage service (S3-compatible). Nmap detected a redirect to port **9001**, which is typically the **MinIO Console (GUI)**. The 400 error leak mentioned a resource path: `/nice ports,/Trinity.txt.bak`. |

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

#### 2.2.1. Update Hosts File

Command: `sudo sh -c 'echo "TARGET_IP facts.htb" >> /etc/hosts'`

Breakdown:

- **`sudo`**
    - **Description:** Superuser Do.
    - **Purpose:** Executes the subsequent command with root privileges. This is required because `/etc/hosts` is a system-protected file that ordinary users cannot modify.
- **`sh -c`**
    - **Description:** Shell Command String.
    - **Purpose:** Tells the system to run a new shell instance and execute the string inside the single quotes. This is used here because the redirection (`>>`) needs root privileges to write to the file; simply using `sudo echo ... >> /etc/hosts` would fail because the redirection is handled by your current (unprivileged) shell.
- **`echo "TARGET_IP facts.htb"`**
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

When you type a URL like `google.com` or `facts.htb` into your browser, your computer needs to translate that text into a numerical IP address. It follows a specific order of operations:

1. **The Browser Cache:** Your browser checks if it already knows the IP from a previous visit.
2. **The Hosts File (`/etc/hosts`):** This is your computer's "private address book." It checks here **first** before asking the internet. If an entry exists, it stops looking and goes to that IP.
3. **DNS Servers:** If the name isn't in your private book, your computer asks a DNS server (like Google’s `8.8.8.8` or your ISP).

##### Why `google.com` works but `facts.htb` doesn't

- **Public Sites:** `google.com` is registered on public DNS servers. When you ask the internet "Where is Google?", the internet has an official answer.

- **HTB Sites:** `facts.htb` is a **private domain** inside the HTB lab environment. Public DNS servers have no idea it exists. Because your computer can't find an "official" record, it gives you a "Server Not Found" error—unless you write the address into your private address book (`/etc/hosts`) yourself.
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>
#### 2.2.2. Web Enumeration

Browse to `http://facts.htb`.

![[Pasted image 20260215184146.png]]
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

#### 2.2.3. Directory Fuzzing

Command: `ffuf -w /usr/share/wordlists/dirb/common.txt -u http://facts.htb/FUZZ`

Breakdown:

- **`-u`**
    - **Description:** Target URL.
    - **Purpose:** Specifies the URL to be fuzzed. The keyword `FUZZ` tells the tool exactly where to inject the words from your wordlist.
- **`-w`**
    - **Description:** Wordlist Path.
    - **Purpose:** Provides the list of common directory and file names (like `admin`, `config`, etc.) to test against the server.

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

                        [Status: 200, Size: 11098, Words: 1328, Lines: 125, Duration: 372ms]
.profile                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 437ms]
.bash_history           [Status: 200, Size: 11137, Words: 1328, Lines: 125, Duration: 547ms]
.bashrc                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 574ms]
.rhosts                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 698ms]
.sh_history             [Status: 200, Size: 11131, Words: 1328, Lines: 125, Duration: 724ms]
.cvs                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 901ms]
.cache                  [Status: 200, Size: 11116, Words: 1328, Lines: 125, Duration: 902ms]
.config                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 1012ms]
.ssh                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1080ms]
.htaccess               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 1204ms]
.listings               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 1271ms]
.svn                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1418ms]
.passwd                 [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 1488ms]
.subversion             [Status: 200, Size: 11131, Words: 1328, Lines: 125, Duration: 1550ms]
.hta                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1561ms]
.cvsignore              [Status: 200, Size: 11128, Words: 1328, Lines: 125, Duration: 1716ms]
.swf                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1864ms]
.mysql_history          [Status: 200, Size: 11140, Words: 1328, Lines: 125, Duration: 2043ms]
.history                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2051ms]
.web                    [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2172ms]
.forward                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 1209ms]
.perf                   [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 2806ms]
.htpasswd               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 1019ms]
.listing                [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 3241ms]
400                     [Status: 200, Size: 6685, Words: 993, Lines: 115, Duration: 2147ms]
404                     [Status: 200, Size: 4836, Words: 832, Lines: 115, Duration: 2014ms]
500                     [Status: 200, Size: 7918, Words: 1035, Lines: 115, Duration: 2058ms]
admin                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2047ms]
admin.cgi               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2126ms]
admin.php               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2013ms]
admin.pl                [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 1994ms]
ajax                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 2918ms]
cache                   [Status: 200, Size: 11116, Words: 1328, Lines: 125, Duration: 2601ms]
captcha                 [Status: 200, Size: 5576, Words: 16, Lines: 14, Duration: 3136ms]
config                  [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 2646ms]
cvs                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2555ms]
CVS                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2526ms]
en                      [Status: 200, Size: 11109, Words: 1328, Lines: 125, Duration: 2140ms]
error                   [Status: 500, Size: 7918, Words: 1035, Lines: 115, Duration: 2264ms]
forward                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2010ms]
history                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2177ms]
hta                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1763ms]
htpasswd                [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 1950ms]
index.php               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2269ms]
index                   [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 2263ms]
index.html              [Status: 200, Size: 11128, Words: 1328, Lines: 125, Duration: 2162ms]
index.htm               [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2118ms]
Index                   [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 2576ms]
listing                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 2149ms]
listings                [Status: 200, Size: 11125, Words: 1328, Lines: 125, Duration: 2175ms]
page                    [Status: 200, Size: 19593, Words: 3296, Lines: 282, Duration: 3227ms]
passwd                  [Status: 200, Size: 11119, Words: 1328, Lines: 125, Duration: 3174ms]
perf                    [Status: 200, Size: 11113, Words: 1328, Lines: 125, Duration: 1301ms]
post                    [Status: 200, Size: 11308, Words: 1414, Lines: 152, Duration: 2327ms]
profile                 [Status: 200, Size: 11122, Words: 1328, Lines: 125, Duration: 1948ms]
robots.txt              [Status: 200, Size: 99, Words: 12, Lines: 2, Duration: 2041ms]
robots                  [Status: 200, Size: 33, Words: 2, Lines: 1, Duration: 2113ms]
rss                     [Status: 200, Size: 183, Words: 20, Lines: 9, Duration: 2380ms]
search                  [Status: 200, Size: 19187, Words: 3276, Lines: 272, Duration: 3275ms]
sitemap                 [Status: 200, Size: 3508, Words: 424, Lines: 130, Duration: 2742ms]
sitemap.xml             [Status: 200, Size: 3508, Words: 424, Lines: 130, Duration: 2390ms]
sitemap.gz              [Status: 500, Size: 7918, Words: 1035, Lines: 115, Duration: 2539ms]
ssh                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 1891ms]
svn                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2477ms]
swf                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2566ms]
up                      [Status: 200, Size: 73, Words: 4, Lines: 1, Duration: 2190ms]
web                     [Status: 200, Size: 11110, Words: 1328, Lines: 125, Duration: 2363ms]
welcome                 [Status: 200, Size: 11966, Words: 1481, Lines: 130, Duration: 3278ms]
:: Progress: [4614/4614] :: Job [1/1] :: 16 req/sec :: Duration: [0:04:42] :: Errors: 0 ::
```

The most interesting hit is `/admin`
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

#### 2.2.4. Visit the Admin Page

![[Pasted image 20260224152519.png]]

Create an account and login

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

