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



2.      Identify the service running on the second port from your nmap scan. What is the version of that service? (2mks)

_Paste screenshot(s) demonstrating the answer here_

3.      There is a hidden flag in the webpage. Submit the contents of the flag (2 mks)

_Paste screenshot(s) demonstrating the answer here_

4.      Perform banner grabbing using netcat on port 1337. Submit the contents of the flag. (2marks)

_Paste screenshot(s) demonstrating the answer here_

5.      The same service is running on more than one port of the system. What is the version of the service? (2 mk)

_Paste screenshot(s) demonstrating the answer here_

6.      Using smbclient tool, identify the available network shares (2 mks)

_Paste screenshot(s) demonstrating the answer here_

7.      How many hidden shares are among the identified shares above? Name them. (2 mks)

_Paste screenshot(s) demonstrating the answer here_

8.      What is the name of the share that is accessible? (2 mk)

_Paste screenshot(s) demonstrating the answer here_

9.      Access the share using null authentication, what is the folder's name discovered within the share? (2 marks)

_Paste screenshot(s) demonstrating the answer here_

10.   Download and unpack the files inside the folder and read the contents. Submit the contents of the flag (2 mks)

_Paste screenshot(s) demonstrating the answer here_

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