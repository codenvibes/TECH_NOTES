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
    - **Command:** `[The Full Command]`
    - **Breakdown:** > * `[Flag/Component]` **Description:** [What it is] | **Purpose:** [Why it was used in this specific CTF context].

3. **Next Steps:** Conclude each entry by suggesting 3 logical next steps or common bypasses to move forward.


**My Input Format:** I will provide: **[What I found]**, **[The command I ran]**, and **[The result]**.

**Do you understand these formatting requirements? If so, let's start with my first step.**



---

previous lesson we successfully executed our first sql map command that found a sql injection vulnerability and exploited it in order to give us all kinds of information about the applications database including table names one of the tables that we saw was the users table which gives us a pretty strong indication that there are probably user's passwords that are stored in there and as a red teamer one of our goals might be to see whether we can get that password information and potentially also crack those passwords so first things first we need to enumerate that table in order to see if we can grab those passwords which will very likely be stored in an encrypted format and which we will have to also try and crack and we can do this by narrowing down sql maps focus to enumerate columns using dash dash columns in just this table with the dash t table option where the table value represents the database table or tables to enumerate we'll also of course pass in the dash dash batch in order to bypass the different prompts and then we'll press enter and then we'll be able to confirm that there is a passwords column in this table so now we're going to use the dump option in order to dump the data that's contained in the users table so we'll have dash dash dump dash t for the table users and then dash dash batch so while this is working we can see that it asks us if we wanted to store hashes to a temporary file for further processing with other tools while this is a really practical feature we're not going to need that here next it asked if we wanted to crack the hashes via a dictionary based attack and the default was yes which means that sql map then launches a dictionary based attack against the hashes that are found in the database and we can see here that by default it uses the wordlist.txt file that's included with kali linux but we could have specified a custom dictionary file or even a file with a list of dictionary files then the final option that it asks is whether we want to use common password suffixes but since it's much slower by default it says no then we can see that it started the attack and shortly after it was able to crack multiple password hashes so now we end up with a neatly formatted table that contains the user id the user the avatar and the password that includes both the hash as well as the cracked passwords and then some other columns in this table so sql map not only enumerated the table and found passwords but it also has a built-in cracker that was able to crack these password hashes and give us the plain text version of that password pretty cool right now one more option that we can use that does something kind of similar is the dash dash passwords option except this time it instructs sql map to both list and crack the database management system users password hashes so not the applications users but the user is in that database management system which means the users that manage the database when the user that we are assuming by running these exploits against the database has read access to the system table that contains the database management users passwords sql map is able to enumerate password hashes for each of those users as we just saw earlier but unfortunately in this environment we do not have the proper permissions so we just can't demonstrate this at this time but this is a good reminder that not everything will work every time even if you're able to find a vulnerability that's why as a bug bounty hunter or pen tester or general security researcher you have to continue pushing and trying to find different angles before giving up what might work in one environment or in one circumstance may not work in other circumstances but the fact remains that these are built-in features in sql map which makes it very practical because it avoids the need to download or use other tools unless we're dealing with a more complex situation we're able to do quite a bit from just this tool alone so now that we've completely compromised this application's user's passwords let's mark this lesson as complete and let's move on to the next hey i hope you enjoyed this short course on sql map for beginners