---
tags:
  - HTB_SN_10
link: https://app.hackthebox.com/machines/Pirate?sort_by=created_at&sort_type=desc
description: Hard·Windows
image: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/5fc0db532017e570ae0daf199e5cd6ac.png
date: 2025-03-12
---
## Machine Information

As is common in real life pentests, you will start the Pirate box with credentials for the following account pentest / p3nt3st2025!&
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

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

### 1.1 Connecting to the HTB VPN

First, standard procedure:

Download your personalized `.ovpn` file from Hack The Box.

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
┌──(kali㉿kali)-[~/PUEMAN/HTB]
└─$ ping -c 4 TARGET_IP                                            
PING TARGET_IP(TARGET_IP) 56(84) bytes of data.
64 bytes from TARGET_IP: icmp_seq=1 ttl=127 time=538 ms
64 bytes from TARGET_IP: icmp_seq=2 ttl=127 time=527 ms
64 bytes from TARGET_IP: icmp_seq=3 ttl=127 time=524 ms
64 bytes from TARGET_IP: icmp_seq=4 ttl=127 time=525 ms

--- TARGET_IPping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 523.584/528.421/537.987/5.698 ms
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

Command: `nmap -p- --min-rate 5000 -Pn TARGET_IP -oG scan1.txt`

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
- **`-oG scan1.txt`**
	- **Description:** Grepable Output.
	- **Purpose:** Saves the scan results in a flat, single-line format that is easy to search using command-line tools like `grep` or `awk`.

Output:

```shell
┌──(kali㉿kali)-[~/PUEMAN/HTB/SN10/Pirate]
└─$ nmap -p- --min-rate 5000 -Pn TARGET_IP -oG scan.txt -oG scan1.txt
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-12 07:46 -0400
Nmap scan report for TARGET_IP
Host is up (0.68s latency).
Not shown: 65513 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
443/tcp   open  https
445/tcp   open  microsoft-ds
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
2179/tcp  open  vmrdp
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
49667/tcp open  unknown
49685/tcp open  unknown
49686/tcp open  unknown
49688/tcp open  unknown
49689/tcp open  unknown
56177/tcp open  unknown
57220/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 73.98 seconds

┌──(kali㉿kali)-[~/PUEMAN/HTB/SN10/Pirate]
└─$ ls
scan1.txt
```
<div align="center">
<br>
<br>
</div>

#### 2.1.2 Port Extraction

**Command:** `ports=$(grep -oP '\d+(?=/open)' scan1.txt | paste -sd, -)`

**Breakdown:**
- **`ports=$(...)`**
    - **Description:** Command Substitution.
    - **Purpose:** Runs the commands inside the parentheses and saves the resulting text into a variable named `$ports` for later use.
- **`grep -oP`**
    - **Description:** Only-matching Perl Regex.
    - **Purpose:** `-o` prints only the matching part of the line; `-P` allows advanced Perl-style regular expressions.
- **`'\d+(?=/open)'`**
    - **Description:** Regex Pattern.
    - **Purpose:** `\d+` finds digits (port numbers). `(?=/open)` is a "lookahead" that ensures the digits are only picked if they are immediately followed by `/open`.
- **`|`**
    - **Description:** The Pipe.
    - **Purpose:** Takes the output of the `grep` command and feeds it as input to the `paste` command.
- **`paste -sd, -`**
    - **Description:** Serial Paste with Delimiter.
    - **Purpose:** `-s` (serial) joins all the individual port lines into one single line. `-d,` sets the comma as the separator between them.
<div align="center">
<br>
<br>
</div>

#### 2.1.3 The "Deep Dive" Scan (Targeted Aggression)

**Command:** `nmap -A -p $ports TARGET_IP`

**Breakdown:**
- **`-A`**   
    - **Description:** Aggressive Scan Mode.       
    - **Purpose:** Enables OS detection, version detection, script scanning (`-sC`), and traceroute all at once.
- `-p $ports`
    - **Description:** Targeted Port List.
    - **Purpose:** Instead of scanning all ports again, this tells Nmap to only scan the specific open ports stored in your variable (e.g., `80,443,22`).

Output:

``` shell
┌──(kali㉿kali)-[~/PUEMAN/HTB/SN10/Pirate]
└─$ nmap -A -p $ports TARGET_IP                         
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-15 04:21 -0400
Nmap scan report for TARGET_IP
Host is up (0.34s latency).

PORT      STATE SERVICE           VERSION
53/tcp    open  domain            Simple DNS Plus
80/tcp    open  http              Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec      Microsoft Windows Kerberos (server time: 2026-03-15 15:21:20Z)
135/tcp   open  msrpc             Microsoft Windows RPC
139/tcp   open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp   open  ldap              Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
|_ssl-date: 2026-03-15T15:23:11+00:00; +6h59m49s from scanner time.
443/tcp   open  https?
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap          Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
|_ssl-date: 2026-03-15T15:23:11+00:00; +6h59m50s from scanner time.
2179/tcp  open  vmrdp?
3268/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)
|_ssl-date: 2026-03-15T15:23:14+00:00; +6h59m49s from scanner time.
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
3269/tcp  open  globalcatLDAPssl?
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
|_ssl-date: 2026-03-15T15:23:13+00:00; +6h59m49s from scanner time.
5985/tcp  open  http              Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf            .NET Message Framing
49667/tcp open  msrpc             Microsoft Windows RPC
49685/tcp open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
49686/tcp open  msrpc             Microsoft Windows RPC
49688/tcp open  msrpc             Microsoft Windows RPC
49689/tcp open  msrpc             Microsoft Windows RPC
49913/tcp open  msrpc             Microsoft Windows RPC
60791/tcp open  msrpc             Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-03-15T15:22:36
|_  start_date: N/A
|_clock-skew: mean: 6h59m49s, deviation: 0s, median: 6h59m48s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

TRACEROUTE (using port 445/tcp)
HOP RTT       ADDRESS
1   350.60 ms 10.10.14.1
2   350.97 ms TARGET_IP

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 133.82 seconds
```
<div align="center">
<br>
<br>
</div>

#### 2.1.4 Scan Results Analysis

| **Port**       | **Service**    | **Version**                    | **Analysis**                                                                                                                           |
| -------------- | -------------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| **53**         | DNS            | Simple DNS Plus                | Essential for domain resolution. May be used for zone transfers or identifying other hosts via `nslookup`.                             |
| **80**         | HTTP           | Microsoft IIS httpd 10.0       | Standard web server. Likely the primary entry point. Requires directory brute-forcing and manual inspection for vulnerabilities.       |
| **88**         | Kerberos       | Microsoft Windows Kerberos     | Confirms the target is a Domain Controller. Susceptible to User Enumeration and "AS-REP Roasting" if pre-authentication is disabled.   |
| **135, 593**   | RPC            | Microsoft Windows RPC          | Endpoint mapper for remote procedure calls. Useful for enumerating system information if the service is poorly protected.              |
| **389, 636**   | LDAP/S         | Microsoft Windows AD LDAP      | The "phonebook" of the domain. Can leak the naming context (`pirate.htb`), user lists, and group memberships.                          |
| **445**        | SMB            | Microsoft-ds (Windows 10/2019) | Server Message Block. Priority target for finding open file shares, sensitive documents, or performing "Relay" attacks.                |
| **5985**       | WinRM          | Microsoft HTTPAPI httpd 2.0    | Windows Remote Management. Often used for remote shells. If credentials are found, this is a likely method for gaining initial access. |
| **3268, 3269** | Global Catalog | Microsoft Windows AD LDAP      | Used for searching the entire Active Directory forest. Provides similar enumeration opportunities as standard LDAP.                    |
|                |                |                                |                                                                                                                                        |
<div align="center">
<br>
</div>

##### Why the Question Mark Appears

During a service scan (`-sV` or `-A`), Nmap sends a series of "probes" (specific data packets) to the open port and waits for a response. It then compares that response against its internal database of thousands of service signatures.

The question mark appears in two specific scenarios:

1. **Incomplete Handshake:** The service responded just enough to suggest what it might be, but it didn't provide a unique "fingerprint" that matches a known signature perfectly.

2. **Ambiguous Response:** The data returned by the port is generic. For example, many services use SSL/TLS; if Nmap sees an encrypted wrapper but cannot see what is inside, it might guess `https?` based on the port number (443) and the presence of SSL.

```
┌──(kali㉿kali)-[~/PUEMAN/THM]
└─$ sudo openvpn download.ovpn 
2026-03-15 08:34:59 DEPRECATED OPTION: --persist-key option ignored. Keys are now always persisted across restarts. 
2026-03-15 08:34:59 Note: --cipher is not set. OpenVPN versions before 2.5 defaulted to BF-CBC as fallback when cipher negotiation failed in this case. If you need this fallback please add '--data-ciphers-fallback BF-CBC' to your configuration and/or add BF-CBC to --data-ciphers. E.g. --data-ciphers DEFAULT:BF-CBC
2026-03-15 08:34:59 OpenVPN 2.7_rc6 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] [DCO]
2026-03-15 08:34:59 library versions: OpenSSL 3.5.4 30 Sep 2025, LZO 2.10
2026-03-15 08:34:59 DCO version: 6.18.9+kali-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.18.9-1kali1 (2026-02-10)
2026-03-15 08:35:00 TCP/UDP: Preserving recently used remote address: [AF_INET]13.216.15.166:1194
2026-03-15 08:35:00 Socket Buffers: R=[212992->212992] S=[212992->212992]
2026-03-15 08:35:00 UDPv4 link local: (not bound)
2026-03-15 08:35:00 UDPv4 link remote: [AF_INET]13.216.15.166:1194
2026-03-15 08:35:00 TLS: Initial packet from [AF_INET]13.216.15.166:1194, sid=fe66e86b fb621a94
2026-03-15 08:35:00 WARNING: this configuration may cache passwords in memory -- use the auth-nocache option to prevent this
2026-03-15 08:35:00 VERIFY OK: depth=1, CN=OpenVPN-CA
2026-03-15 08:35:00 VERIFY KU OK
2026-03-15 08:35:00 Validating certificate extended key usage
2026-03-15 08:35:00 ++ Certificate has EKU (str) TLS Web Server Authentication, expects TLS Web Server Authentication
2026-03-15 08:35:00 VERIFY EKU OK
2026-03-15 08:35:00 VERIFY X509NAME OK: CN=openvpn-server
2026-03-15 08:35:00 VERIFY OK: depth=0, CN=openvpn-server
2026-03-15 08:35:02 Control Channel: TLSv1.3, cipher TLSv1.3 TLS_AES_256_GCM_SHA384, peer certificate: 2048 bits RSA, signature: RSA-SHA256, peer temporary key: 253 bits X25519, peer signing digest/type: rsa_pss_rsae_sha256 RSASSA-PSS, key agreement: x25519
2026-03-15 08:35:02 [openvpn-server] Peer Connection Initiated with [AF_INET]13.216.15.166:1194
2026-03-15 08:35:02 TLS: move_session: dest=TM_ACTIVE src=TM_INITIAL reinit_src=1
2026-03-15 08:35:02 TLS: tls_multi_process: initial untrusted session promoted to trusted
2026-03-15 08:35:03 SENT CONTROL [openvpn-server]: 'PUSH_REQUEST' (status=1)
2026-03-15 08:35:03 PUSH: Received control message: 'PUSH_REPLY,route 10.64.0.0 255.240.0.0,tun-mtu 1380,mssfix 1320,route-gateway 192.168.128.1,topology subnet,ping 10,ping-restart 120,ifconfig 192.168.175.13 255.255.128.0,peer-id 158,cipher AES-256-GCM,protocol-flags cc-exit tls-ekm dyn-tls-crypt,tun-mtu 1380'
2026-03-15 08:35:03 Options error: option 'mssfix' cannot be used in this context ([PUSH-OPTIONS])
2026-03-15 08:35:03 OPTIONS IMPORT: --ifconfig/up options modified
2026-03-15 08:35:03 OPTIONS IMPORT: route options modified
2026-03-15 08:35:03 OPTIONS IMPORT: route-related options modified
2026-03-15 08:35:03 OPTIONS IMPORT: tun-mtu set to 1380
2026-03-15 08:35:03 net_route_v4_best_gw query: dst 0.0.0.0
2026-03-15 08:35:03 net_route_v4_best_gw result: via 10.0.2.2 dev eth0
2026-03-15 08:35:03 net_route_v4_best_gw query: dst 13.216.15.166
2026-03-15 08:35:03 net_route_v4_best_gw result: via 10.0.2.2 dev eth0
2026-03-15 08:35:03 net_iface_new: add tun0 type ovpn
2026-03-15 08:35:03 DCO device tun0 opened
2026-03-15 08:35:03 ovpn-dco device [tun0] opened
2026-03-15 08:35:03 net_iface_mtu_set: mtu 1380 for tun0
2026-03-15 08:35:03 net_iface_up: set tun0 up
2026-03-15 08:35:03 net_addr_v4_add: 192.168.175.13/17 dev tun0
2026-03-15 08:35:03 net_route_v4_add: 10.64.0.0/12 via 192.168.128.1 dev [NULL] table 0 metric 200
2026-03-15 08:35:03 Initialization Sequence Completed
2026-03-15 08:35:03 Data Channel: cipher 'AES-256-GCM', peer-id: 158
2026-03-15 08:35:03 Timers: ping 10, ping-restart 120
2026-03-15 08:35:03 Protocol options: explicit-exit-notify 1, protocol-flags cc-exit tls-ekm dyn-tls-crypt

```
<div align="center">
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
<br>
</div>

### 2.2 
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

