🛡️ DAY 48 of my #CYBERSECURITY Journey!
Completed the WingData box on Hack The Box today.



AD4MPU3MAN

----

☁️ DAY 4 of my #CLOUD Journey!
Completed the Compute section of the @Oracle OCI Foundations course:
Topics:  
- Instances & scaling
- Cloud Shell demo
- Creating compute instances
- OKE & container workloads
- Serverless with Oracle Functions
#CloudComputing


---

I am documenting the ____ CTF challenge and need help converting my raw notes into a professional technical report.

**Your Instructions:**

1. **Professional Narrative:** Rewrite my progress into a clear, technical narrative. Avoid repetitive sentence starters (e.g., don't start every sentence with "Next, I..."). Use active, varied transitions like "Initial reconnaissance revealed...", "To further investigate the surface area...", or "Leveraging the identified vulnerability...".

2. **Mandatory Command Breakdown:** Every time a command is mentioned, you **must** document it using this specific format:
    - **Command:** `The Full Command`
    - **Breakdown:** > * `Flag/Component` 
	    - **Description:** What it is
	    - **Purpose:** Why it was used in this specific CTF context.

3. **Next Steps:** Conclude each entry by suggesting 3 logical next steps or common bypasses to move forward.


**My Input Format:** I will provide: What I found, The command I ran, and The result.

**Do you understand these formatting requirements? If so, let's start with my first step.**



---




1. Conduct an Nmap scan on the provided Linux machine. Identify the open ports. (2 mks)

```shell
┌──(kali㉿kali)-[~]
└─$ nmap 4.180.20.166    
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-07 04:24 -0500
Nmap scan report for 4.180.20.166
Host is up (0.027s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 10.08 seconds
```

2. Identify the service running on the second port from your nmap scan. What is the version of that service? (2mks)

```shell
┌──(kali㉿kali)-[~]
└─$ nmap -sV -p 80 4.180.20.166          
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-07 04:25 -0500
Nmap scan report for 4.180.20.166
Host is up (0.016s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.24.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.49 seconds
```

3.      There is a hidden flag in the webpage. Submit the contents of the flag (2 mks)

```shell

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Cyber Resilience CTF — Exam Portal</title>
  <style>
    body { font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; background:#0b1220; color:#e6eef8; margin:0; padding:3rem; }
    .card { background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border:1px solid rgba(255,255,255,0.04); padding:2rem; border-radius:12px; max-width:900px; margin: 0 auto; box-shadow: 0 10px 30px rgba(0,0,0,0.6); }
    h1 { margin-top:0; color:#a8d1ff;}
    .hint { margin-top:1rem; color:#cbe6ff; font-size:0.95rem; }
    footer { margin-top:2rem; font-size:0.8rem; color:#94b6df }
    .banner { font-weight:600; color:#ffd47a; }
  </style>
</head>
<body>
  <div class="card">
    <h1>Welcome to the Cyber Resilience Exam Portal</h1>
    <p class="banner">Your task: enumerate services, extract flags, and document findings.</p>
    <p>Start by scanning the host, checking open services, and inspecting any accessible shares. This portal is intentionally configured with multiple hints.</p>
    <div class="hint">
      Hint B: Console-savvy students should remember to check the browser console for additional leads.
    </div>

    <hr/>

    <h3>Rules</h3>
    <ol>
      <li>Work only inside the designated directories and shares.</li>
      <li>Do NOT attempt to break out of the environment.</li>
      <li>Report any bugs to the exam admins.</li>
    </ol>

    <footer>Exam environment — do not share flags outside this exercise.</footer>
  </div>

  <!-- hidden flag: shujaa{v13w_s0urc3_m4st3r} -->

  <script>
    // Bonus flag for console hunters
    console.log("shujaa{c0ns0l3_d3t3ct1v3}");
  </script>
</body>
</html>
```

4.      Perform banner grabbing using netcat on port 1337. Submit the contents of the flag. (2marks)

```shell
┌──(kali㉿kali)-[~]
└─$ nc -vn 4.180.20.166 1337
(UNKNOWN) [4.180.20.166] 1337 (?) open
CTFService v1.2 - Welcome to the exam.\nFLAG: shujaa{n3tc4t_l1st3n3r_fl4g}\n^C
```

5.      The same service is running on more than one port of the system. What is the version of the service? (2 mk)

```shell
└─$ nmap -sV 4.180.20.166                 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-07 04:53 -0500
Nmap scan report for 4.180.20.166
Host is up (0.025s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http        nginx 1.24.0 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 4
445/tcp open  netbios-ssn Samba smbd 4
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.44 seconds
```

6.      Using smbclient tool, identify the available network shares (2 mks)

```shell
┌──(kali㉿kali)-[~]
└─$ smbclient -L 4.180.20.166 -N         

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        CTFShare        Disk      CTF
        IPC$            IPC       IPC Service (CTF Samba Server)
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
Protocol negotiation to server 4.180.20.166 (for a protocol between LANMAN1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```

7. How many hidden shares are among the identified shares above? Name them. (2 mks)
	2

8. What is the name of the share that is accessible? (2 mk)
	CTFShare

9. Access the share using null authentication, what is the folder's name discovered within the share? (2 marks)

```shell
┌──(kali㉿kali)-[~]
└─$ smbclient //4.180.20.166/CTFShare -N 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Feb 26 04:40:59 2026
  ..                                  D        0  Thu Feb 26 04:40:59 2026
  confidential.zip                    N      383  Thu Feb 26 04:40:59 2026

                29379712 blocks of size 1024. 26685792 blocks available
smb: \> 
```

10. Download and unpack the files inside the folder and read the contents. Submit the contents of the flag (2 mks)

```shell
┌──(kali㉿kali)-[~]
└─$ smbclient //4.180.20.166/CTFShare -N
Try "help" to get a list of possible commands.
smb: \> get confidential.zip
getting file \confidential.zip of size 383 as confidential.zip (0.7 KiloBytes/sec) (average 0.7 KiloBytes/sec)
```

```shell
┌──(kali㉿kali)-[~/pueman/Shujaa]
└─$ unzip confidential.zip 
Archive:  confidential.zip
 extracting: flag.txt                
 extracting: creds.txt               
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/pueman/Shujaa]
└─$ ls
confidential.zip  creds.txt  flag.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/pueman/Shujaa]
└─$ cat creds.txt flag.txt 
username: examuser
password: Cyb3rShuj44!
shujaa{smb_sh4r3_3numer4t3d}
```

11.   What is the exposed username and password? (1 mk)

_Paste screenshot(s) demonstrating the answer here_

12.   SSH into the machine and retrieve the flag in the user’s home directory. (2 mks)

_Paste screenshot(s) demonstrating the answer here_

13.   In the user's home directory what is the name of the first hidden file owned by root. (1 mk)

_Paste screenshot(s) demonstrating the answer here_

14.   Retrieve the flag by decoding the contents of the file you found above. **[NB only use the terminal to solve this task]** (2mks)

_Paste screenshot(s) demonstrating the answer here_

15.   Using grep, retrieve a flag hidden in the grepme.txt within the user's home directory (2 mks)

_Paste screenshot(s) demonstrating the answer here_

16.   Create a file with the content cybershujaa_exam, save the file, run the binary (checkflag) against your file and retrieve the flag. (3 mks)

_Paste screenshot(s) demonstrating the answer here_

17.   Create a NEW file called "compressed.txt" with the content "zipmaster2024", compress it then run the binary in the user's home directory called "checkifcompressed" giving the name of your zip file as an argument. What is the flag? (3 mks)

_Paste screenshot(s) demonstrating the answer here_

18.  A misconfiguration is on the shadow file allowing users to read its contents. Retrieve both the password file passwd and the shadow file. (2 mks)

19.  Unshadow and crack using John. What is the examadmin password? Use the provided wordlist. (2 mks) HINT: use the format –format=crypt

_Paste screenshot(s) demonstrating the answer here_

20.   Retrieve the  flag.txt from the examadmin user’s home directory. (2 mks)

_Paste screenshot(s) demonstrating the answer here_