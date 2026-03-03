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

the latest image then you should have a very up-to-date sql map installation with that said it might not be the latest version of sql map as you can see here our version of sql map is 1.10.2 stable 

the term stable can mean a few different things when it comes to software and it's up to the developers to define its meaning but it typically means that the version has ironed out most of the major bugs and issues and so it is the intended version to be used by most users so this is a good version to use but again we can check and see if there are any newer versions by going to the project's github repository and checking releases 

and we can see that I'm using the latest version but if that was not the case you can go ahead and download the new version to you cali installation and to do that we can use a few different methods including by downloading archives or by cloning the git repository a benefit of cloning the repository is that you can easily update your version by pulling changes through git at a later time versus if you downloaded an archive that archive is quote-unquote stuck in time so you'd have to regularly go back and download the latest archive in order to update that version so it's up to you 

the cutting edge and there may be some unknown or known bugs but it also means that you get the latest features so it is a trade-off to be aware of and the version that you choose is ultimately up to you 

for this demo we will stick to the default installation of sql map to minimize the risk of bugs and issues along the way but feel free to try using this latest version instead just be aware that there may be some slight differences so now that we've verified that we have a working installation of sql map with two different versions and we've learned how to download the latest version let's go ahead and use sql map for the very first time

## Using sqlmap for the first time

whenever I download new command line tools there are a few things that i like to do number one i like to open up the tools documentation which is usually on github at least for open source tools and then number two i like to use the dash h option in my terminal dash h typically gives you a really good overview of some of the most popular and useful commands or options that we can use with this particular tool and it's usually in an easily digestible format which makes it easier to start with so combining that with the tools documentation which can sometimes be hit or miss and we can be off to a really good start within a matter of minutes luckily sql maps documentation is actually really really good for this type of open source project 

let's start by looking over the introduction section of the tools documentation on github on this page they give us a very simple example of what a target url might look like for sql injection 

here we have a target ip address a path to an endpoint and a query parameter of id equals one 

so to check for sql injection we might alter that parameter value from being just one to being something different like one and one equals one or one and one equals two 

and then watching for the result on the webpage or via the http request and this is something that we can do manually or that we can use a tool like zap or burp to help with but of course this is also something that sql map can help with by passing in that same target to sql map the tool will number one identify the parameters to test so in this case the id number two identify which sql injection techniques can be used to try and exploit that parameter number three fingerprint the backend database management system to gather information about what technologies we're dealing with and then finally depending on what it finds attempt to exploit vulnerabilities so that's great and that sounds awesome but how do we go beyond concepts and actually put this into practice well we can head over to the usage section of this documentation which includes a massive comprehensive list of all the sql map options and how to use them but that can be overwhelming to beginners so instead let's go back to the command line and let's use the dash h option to help us get started scrolling to the top right away we can see a list of options for target as we saw with the prior example we want to be able to tell sql map which target to go after and we can use the very first option of dash u or dash dash url in order to specify that this is where we enter our url to go after which i will show in just a moment then we see a section for request which lets us specify how we want sql map to connect to our target url this is important to understand because different requests and targets require different approaches if it's a get request that's different than if it's a post request if it's a post request we'll need to pass in additional data with dash dash data equals all caps data where that all caps data is a string to be sent through the post request we may also need to pass in cookies with dash dash cookie equals cookie where the all caps is an http cookie header value because if we're going after an application that requires authentication and the authentication uses cookies for example then our sql map requests will not go through and will get rejected by the application unless we also provide that cookie information another really important set of options to know is under the enumeration category these options basically tell sql map what to do and what to return if it finds a vulnerability that it can exploit or if it finds information that is not properly concealed for example you can ask it to retrieve the current database management system user with dash dash current user you can ask for a list of the database tables with dash dash tables the entire database schema with schema and a whole bunch more this is a really powerful set of options that we'll definitely use in this course and that we use in the more advanced version of this course as well but finally before completing this lesson and moving on to the next where we will issue our first command let's take a look at the wizard option that's provided by sql map this option is really only useful when you're first getting started with sql map because once you have more experience you can work faster and issue more powerful commands without using the wizard but this option basically spoon feeds you what it needs to properly attack a target so let's try it even though we don't have an environment running yet just to see what it does so it will ask us to enter the full target url we'll just put localhost in here then it asks for post data but if there's none you can just press enter which is the default then it will ask for injection difficulty which changes both the level and risk options with a default of one for normal so we'll just do that and then again it asks us for enumeration we'll choose just the basic enumeration options at which point sql map will start to run but you'll see that it fails because again we have no environment running on our localhost but we get to see how it asks us questions to determine what it is that we need again this is just a simpler way of using the same options and commands that we would issue via the terminal instead so feel free to use the wizard throughout the course if you'd like but we're basically not really going to use it instead we're going to go straight into using the options that are available to us but now that we've taken a look at the main available options to get us started in using sql map let's complete this lesson and let's move on to the next where we will apply what we just learned in order to issue our first sql map command and find as well as exploit our first sql injection with sql map so i'll see you there