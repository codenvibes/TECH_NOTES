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

## Downloading & installing the latest sqlmap

if you're using the latest image of kali should have a very up-to-date sql map installation with that said it might not be the latest version of sql map. My version of sql map is 1.10.2 stable 

the term stable can mean a few different things when it comes to software and it's up to the developers to define its meaning but it typically means that the version has ironed out most of the major bugs and issues and so it is the intended version to be used by most users so this is a good version to use but again we can check and see if there are any newer versions by going to the project's github repository and checking releases 

and we can see that I'm using the latest version but if that was not the case you can go ahead and download the new version to you cali installation and to do that we can use a few different methods including downloading archives or by cloning the git repository a benefit of cloning the repository is that you can easily update your version by pulling changes through git at a later time versus if you downloaded an archive that archive is quote-unquote stuck in time so you'd have to regularly go back and download the latest archive in order to update that version so it's up to you 

the cutting edge may have some unknown or known bugs but it also means that you get the latest features so it is a trade-off to be aware of and like I said the version that you choose is ultimately up to you 

for this demo we will stick to the default installation of sql map to minimize the risk of bugs and issues along the way but feel free to try using this latest version instead just be aware that there may be some slight differences so now that we've verified that we have a working installation of sql map and we've learned how to download the latest version let's go ahead and use sql map for the very first time

## Using sqlmap for the first time

whenever I download new command line tools there are a few things that i like to do number one i like to open up the tools documentation which is usually on github at least for open source tools and then number two i like to use the dash h option in my terminal dash h typically gives you a really good overview of some of the most popular and useful commands or options that we can use with this particular tool and it's usually in an easily digestible format which makes it easier to start with so combining that with the tools documentation which can sometimes be hit or miss and we can be off to a really good start within a matter of minutes luckily sql maps documentation is actually really really good for this type of open source project 

let's start by looking over the introduction section of the tools documentation on github on this page they give us a very simple example of what a target url might look like for sql injection 

here we have a target ip address a path to an endpoint and a query parameter of id equals one 

so to check for sql injection we might alter that parameter value from being just one to being something different like one and one equals one or one and one equals two 

and then watching for the result on the webpage or via the http request and this is something that we can do manually or that we can use a tool like zap or burp to help with 

but of course this is also something that sql map can help with by passing in that same target to sql map the tool will 

number one identify the parameters to test so in this case the id 

number two identify which sql injection techniques can be used to try and exploit that parameter 

number three fingerprint the backend database management system to gather information about what technologies we're dealing with 

and then finally depending on what it finds attempt to exploit vulnerabilities so that's great and that sounds awesome but how do we go beyond concepts and actually put this into practice 

well we can head over to the usage section of this documentation which includes a massive comprehensive list of all the sql map options and how to use them 

but that can be overwhelming to beginners so instead let's go back to the command line and let's use the dash h option to help us get started 

scrolling to the top right away we can see a list of options for target as we saw with the prior example we want to be able to tell sql map which target to go after and we can use the very first option of dash u or dash dash url in order to specify that this is where we enter our url to go after which i will show in just a moment 

then we see a section for request which lets us specify how we want sql map to connect to our target url this is important to understand because different requests and targets require different approaches if it's a get request that's different than if it's a post request if it's a post request we'll need to pass in additional data with dash dash data equals all caps data where that all caps data is a string to be sent through the post request 

we may also need to pass in cookies with dash dash cookie equals cookie where the all caps is an http cookie header value because if we're going after an application that requires authentication and the authentication uses cookies for example then our sql map requests will not go through and will get rejected by the application unless we also provide that cookie information 

another really important set of options to know is under the enumeration category these options basically tell sql map what to do and what to return if it finds a vulnerability that it can exploit or if it finds information that is not properly concealed for example you can ask it to retrieve the current database management system user with dash dash current user 

you can ask for a list of the database tables with dash dash tables 

the entire database schema with schema and a whole bunch more 

finally before we issue our first command let's take a look at the wizard option that's provided by sql map this option is really only useful when you're first getting started with sql map because once you have more experience you can work faster and issue more powerful commands without using the wizard but this option basically spoon feeds you what it needs to properly attack a target so let's try it even though we don't have an environment running yet just to see what it does

so it will ask us to enter the full target url we'll just put localhost in here 

then it asks for post data but if there's none you can just press enter which is the default 

then it will ask for injection difficulty which changes both the level and risk options with a default of one for normal so we'll just do that 

and then again it asks us for enumeration we'll choose just the basic enumeration options at which point sql map will start to run but you'll see that it fails because again we have no environment running on our localhost but we get to see how it asks us questions to determine what it is that we need again this is just a simpler way of using the same options and commands that we would issue via the terminal instead so feel free to use the wizard throughout the demo if you'd like but we're basically not really going to use it instead we're going to go straight into using the options that are available to us 

now that we've taken a look at the main available options to get us started in using sql map let's move on to applying what we just learned in order to issue our first sql map command and find as well as exploit our first sql injection with sql map 


we actually have enough to issue our first command against a vulnerable target environment so let's see what we can do first things 


first let's turn on our target environment

we'll do a docker run of the vulnerables web dvwa 

once that's up and running we want to log into the application with admin password then we'll create the database and then we will re-log in with the same credentials 

so now that our environment is on let's navigate to our target endpoint by going to sql injections 

at this specific endpoint you can see that we have a user input field asking for a user id and if we type in a random number and we click on submit you'll notice that the url changes and this gives us a hint that it's probably a get request which we can confirm by opening up our devtools using f12 on our keyboard and then going to the network tab and then resubmitting a similar request 

then we can see that it is indeed a get request and when we click on it we'll get additional information about that request let's leave this open for now and let's open up another terminal to use sql map from 

as we know we need to pass in a target url with the dash u option so let's copy this entire url from our browser into the terminal let's paste the url including the parameter and let's paste it in our terminal in between quotes so we have sql map dash u quote and then the target endpoint and then end quote 

now in some cases you may have to specify a port in addition to the target url especially if it's not running on the default http or https ports of 80 and 443 respectively because otherwise sql map will try to use those default ports and it won't be able to establish a connection but in this case we are using port 80 and we specified http so there's really no need to specify the port so let's submit this request now even though it won't work 

and we see that it says that it's testing a connection to the target url but it got a 302 redirect to the login.php page and it's asking us if we want to follow that redirect but we'll press ctrl or command c to get out of that prompt 

now if you remember we had to log into the application in order to access this endpoint so because we are behind a gated page we need to give sql maps the options it needs to properly authenticate its calls and in the case of this application it's really easy because it just uses two simple cookies 

so going back to our browser and the devtools we can see exactly what those cookies are by clicking on the cookies tab and there we'll find the php session id cookie as well as a security of low cookie 

so the php session id will look different on your machine than it does on mine so don't copy and paste mine or that won't work instead you want to copy and paste your own 

since we need to pass in cookie information to sql map let's go ahead and add that to our command so we'll reuse the command we just issued prior to this but then we'll add dash dash cookie equals and then in between quotes we'll put php sesh id exactly as it's spelt and then we will copy and paste after an equal sign then we'll put a semicolon symbol and then we will do security equals low and then again end quote 

so now that we've passed in the cookie sql map knows how to authenticate against this application and the application will let sql map request through so this should work now but let's add one more option to this command the dash dash tables this is an enumeration option that we saw from the help menu that gives sql map a specific task to perform so in this case the dash dash tables will make sql map enumerate the database tables meaning that it will crawl the database structure and information to find and then return the names of all the tables contained in this database so our final command looks like this 

we'll press enter and we'll let it do its thing 

with how we entered this command sql map will periodically stop and prompt us to make a decision here it's saying that the backend database management system is mysql so it's asking if it wants us to skip test payloads specific to other database management systems which makes sense if we know it's my sequel there's really no point in using unrelated payloads it will just add extra overhead and extra time 

so we'll say yes which by the way you'll notice is the default because it's already capitalized again it asks us another question do we want to include all the tests for mysql let's go ahead and say yes which again is the default 

now as sql map is running let's go ahead and take a look at our terminal that's running the container you'll notice requests coming through from sql map these are the requests that are being sent by sql map to our server or in this case our local container so you can see the exact payloads that are being submitted but once again it asks us a question and this can get really annoying especially if you don't have any reason to doubt the defaults so i'll show you how to skip this next time that you run a sql map command 

but for now let's go ahead and answer no with n since it says that the parameter id is vulnerable so there's really no need for us to keep testing other parameters since there aren't any other parameters anyway 

so scrolling back up to where we entered with no we can see additional information about injection points successful payloads and then information that got returned from sql map like the database management system which again was my sequel the database names of dvwa and information schema as well as the tables in those databases so for the dvwa you have two tables you have guest book and then you have users and then the information schema which is critical to helping my sql function has a bunch of other tables 

so this information is returned since we specifically asked for database tables to be enumerated so sql map did exactly as we asked it attacked our target endpoint it found a sql injection vulnerability via the id parameter it exploited that vulnerability and then it enumerated the databases tables 

now before we complete this section let me show you a quick way to bypass those really annoying prompts that have defaults using just a simple option this time let's also enumerate the database schema instead of tables just to switch it up a little bit 

so we'll do dash dash schema and then we will add dash dash batch adding that batch option is what's going to automatically use default behavior instead of asking for user input 

so once we do that we can watch it do its thing and then it returns the results back containing the database schema 

so this gives us a ton of information that will help us write a report back to our client about the vulnerability the information disclosure and other potential exploitations if you've got experience with sql injections by hand it's also quite impressive how it managed to do all of this automatically all it needed from us were a few options and configurations and it handled the rest getting this much information and getting this far would have taken far longer if we were to do it by hand so this is a really great start to using sql map but there's still more that we can do 


## Extracting & cracking passwords from the database

we've successfully executed our first sql map commands that found a sql injection vulnerability and exploited it in order to give us all kinds of information about the applications database including table names one of the tables that we saw was the users table which gives us a pretty strong indication that there are probably user's passwords that are stored in there and as a red teamer one of our goals might be to see whether we can get that password information and potentially also crack those passwords 

so first things first we need to enumerate that table in order to see if we can grab those passwords which will very likely be stored in an encrypted format and which we will have to also try and crack and we can do this by narrowing down sql maps focus to enumerate columns using dash dash columns in just this table with the dash t table option where the table value represents the database table or tables to enumerate we'll also of course pass in the dash dash batch in order to bypass the different prompts and then we'll press enter 

```shell
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://127.0.0.1/vulnerabilities/sqli/?id=435&Submit=Submit#" --cookie="PHPSESSID=avv7jjurfmktgj260nourqsj76; security=low" --columns -T users --batch

```

and then we'll be able to confirm that there is a passwords column in this table 

so now we're going to use the dump option in order to dump the data that's contained in the users table so we'll have dash dash dump dash t for the table users and then dash dash batch 



so while this is working we can see that it asks us if we wanted to store hashes to a temporary file for further processing with other tools while this is a really practical feature we're not going to need that here next it asked if we wanted to crack the hashes via a dictionary based attack and the default was yes which means that sql map then launches a dictionary based attack against the hashes that are found in the database and we can see here that by default it uses the wordlist.txt file that's included with kali linux but we could have specified a custom dictionary file or even a file with a list of dictionary files then the final option that it asks is whether we want to use common password suffixes but since it's much slower by default it says no then we can see that it started the attack and shortly after it was able to crack multiple password hashes so now we end up with a neatly formatted table that contains the user id the user the avatar and the password that includes both the hash as well as the cracked passwords and then some other columns in this table so sql map not only enumerated the table and found passwords but it also has a built-in cracker that was able to crack these password hashes and give us the plain text version of that password pretty cool right now one more option that we can use that does something kind of similar is the dash dash passwords option except this time it instructs sql map to both list and crack the database management system users password hashes so not the applications users but the user is in that database management system which means the users that manage the database when the user that we are assuming by running these exploits against the database has read access to the system table that contains the database management users passwords sql map is able to enumerate password hashes for each of those users as we just saw earlier but unfortunately in this environment we do not have the proper permissions so we just can't demonstrate this at this time but this is a good reminder that not everything will work every time even if you're able to find a vulnerability that's why as a bug bounty hunter or pen tester or general security researcher you have to continue pushing and trying to find different angles before giving up what might work in one environment or in one circumstance may not work in other circumstances but the fact remains that these are built-in features in sql map which makes it very practical because it avoids the need to download or use other tools unless we're dealing with a more complex situation we're able to do quite a bit from just this tool alone so now that we've completely compromised this application's user's passwords let's mark this lesson as complete and let's move on to the next hey i hope you enjoyed this short course on sql map for beginners