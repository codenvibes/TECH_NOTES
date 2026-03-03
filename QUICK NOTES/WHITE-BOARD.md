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

0:00

one of the most devastating and common web attacks today is sql injections they've been around for years and

0:05

they've been the result of some of the most serious data breaches in history why because databases are at the heart

0:11

of web applications and they're filled with valuable data so you might think that all organizations

0:17

and all applications would make defending against these types of attacks a top priority but that's simply not the case sql

0:23

injections are still prevalent in fact an akamai report tracking the top web attack vectors

0:29

from november 2017 to march 2019 found that sql injections represent nearly

0:34

two-thirds of all web application attacks so a few months ago i launched an sql injection

0:40

attacks course that quickly gathered over 17 000 students then i started getting questions about

0:46

sql map so i decided to make this course in this course we start out by learning what sql map is

0:52

then we move on to creating a home lab environment so that we can use sql map against vulnerable environments

0:57

and find our first sql injections we'll also take a look at some of the most common and most useful features

1:03

that sql map has to offer especially for beginners because i show you exactly how to set up your own home lab you'll be able to

1:10

follow along with everything that i do we'll have the exact same environments and the exact same tools to work with

1:15

that i do and all of them are free and by the way once you complete this introductory

1:20

level course we have a more advanced sql map deep dive course that you can check out and use

1:26

to take your skills to the next level now i might still be in development depending on when you're watching this but be sure to check it out so let's get

1:32

started and i'll see you in the course before we jump in and learn how to use sql map it's important that we

## About sqlmap

1:37

understand what it is and just as importantly what it's not sql map is an automatic sql injection

1:44

and database takeover tool it's an open source tool meaning that you can view its entire code base and this code base is maintained by a

1:50

group of contributors including the original authors you can contribute to the project and you can even create your own version of

1:56

it if you wanted to that's the beauty of open source sql map is designed as a penetration testing

2:02

tool that automates the process of detecting and exploiting sql injection flaws and once sql

2:08

injection flaws are found this tool can help you exploit the vulnerabilities to their maximum extent

2:13

by using a large number of different features and in some cases sqlmap can help you completely take over

2:19

database servers so in terms of sql injections this really is a powerful tool it includes

2:24

features like database fingerprinting which is gathering information about what database engine

2:30

version etc an application is using which is really useful for reconnaissance

2:35

it also includes full support from major database engines like mysql oracle postgres sql server sqlite

2:43

mariahdb redshift and a bunch of others it has full support for six different

2:49

sql injection techniques including boolean based blind time-based blind error-based union query based stack

2:56

queries and out-of-band now if you're not familiar with what these techniques are how they work or what they do then be

3:03

sure to check out my free injection attacks course where we cover all these concepts in great detail it also has support to

3:09

enumerate users password hashes privileges roles databases tables and even columns and enumeration

3:16

gathers additional information from the database in order to then exploit it and hopefully at least if you're on the

3:22

red team reveal all of that information i just mentioned it also has support to

3:27

download and upload files as well as execute arbitrary commands on the database server

3:33

and the last thing i'll mention is that it has support to establish out-of-band connections between an

3:39

attacker machine and the database server which is a more advanced technique that makes connections

3:44

from the databases server to your own remote server in order to retrieve results or execute

3:50

commands now there are other features that we didn't mention but these are some of the big ones while it sounds like a magical tool and

3:56

to some degree it is pretty magical it's still not a silver bullet for one while it automates a lot it still

4:02

requires human interaction and human skill to point it in the right direction and to properly configure it

4:08

so just like any other tool it has its limitations and it absolutely should be part of your arsenal especially if you're going to be a web

4:14

pen tester but you can't simply just let it loose on a website find nothing and claim that there are no

4:20

sql injections that won't guarantee that there are no vulnerabilities it will just help speed up the process and

4:26

potentially find vulnerabilities that you wouldn't have manually found it can also be a good tool to add to your

4:32

automated workflow when you're deploying new code which again can help augment your team and help as part of your devops pipeline

4:39

now i saw these questions when doing a quick google search for sql maps so i figured that we would kick off the

4:45

course by answering them since you likely also have these questions the first question was is sql map

4:51

illegal the short answer is no sql map is perfectly legal the long answer is that it depends

4:57

just like buying owning and using a knife in and of itself is perfectly legal going and stabbing someone is not legal

5:05

so the moral of the story is that how we're going to use sql map in this course is perfectly legal

5:10

however if you start to try and use sql map against environments that you do not have explicit written

5:16

permissions for then you enter into illegal territory and you should definitely not do that

5:21

always have permission the second question was what is the purpose of sql map we already answered this one so i'll go

5:27

ahead and skip it the next question was can sql map be traced this answer also

5:32

depends on what you mean by traced and also what configurations you use for sql map but if we go to the typical

5:39

meaning of traced in cyber security at least meaning that someone could find out you used sql map against their platform

5:46

then with default sql map configurations yes you would be traceable however there are other options that you

5:52

can use to mask your traces such as using a tor setting that tunnels your traffic through tor

5:58

which means that you are just as untraceable as any other use of tor now privacy and anonymization is a

6:05

really big and complex topic that is outside the scope of this course so we won't get into that here but

6:10

that's my answer for now and in the last one it's kind of weird it's what is sql map in turmoix

6:15

turmox is an android terminal emulator and linux environment application so it sounds like people were wondering

6:21

what sql map was when they were using that and it's no different than what we've already discussed

6:26

okay so that's it on this lesson for what sql map is let's go ahead and complete it and i'll see you in the next

6:31

one hey really quickly i did want to highlight some prerequisites that you should have in order to move on with this course in

## Pre-requisites

6:38

order to fully utilize sql map it's important that you have a solid foundation in these areas the first one is sql

6:45

because you should know sql pretty well you don't have to be a database administrator but if you don't know what

6:50

i mean by sql then i would stop here and i would go find a course dedicated to learning sql after that i

6:57

would recommend that you check out my injection attacks course because you'll also need to learn about sql injections

7:03

before you can really understand this tool otherwise it's kind of like buying a car when you've never driven one

7:08

before yes technically you can try to drive it but you're probably going to crash the second thing i recommend having a

7:14

solid foundation for is databases like what they are how they work and the different database engines

7:20

as well as their differences so for example how does mysql work versus something like sqlite

7:26

now you don't have to be an expert in this of course but if you don't understand what i mean by database engines that would be a big red flag stop here

7:33

and go find a database course the last thing i'll mention is web or software development

7:38

at least have an understanding of how applications are built how they're structured and how they use

7:44

databases because otherwise finding sql injections is going to be very difficult and you need to be able

7:49

to find potential areas of attack to properly configure and use sql map so

7:54

i would say that those are the three main areas that you should be at least familiar with before taking this course

8:00

and if anything i said in this lesson is foreign to you and you don't understand it or you're rusty because it's been a

8:05

while since maybe you've touched sql then i would just try and brush up on those skills first and then come back

8:12

and keep going so that's it if you feel like you've got all the prerequisites go ahead and complete this lesson

8:17

and i'll see you in the next in this lesson let's walk through setting up our environment in order to follow along

## Create a home lab environment

8:22

with hands-on demonstrations throughout this course this is an important lesson to complete if you want to apply what you're learning hands-on

8:29

which i highly recommend so if you get stuck at any point in time please feel free to reach out and i'll help you resolve the issue

8:35

so that you can move on now the first thing we need to configure is kali linux which is a free open source linux

8:41

distribution that's often used for digital forensics and penetration testing now the reason that we want to use cali

8:46

apart from its popularity is because it comes pre-installed with sql map and so that way we're using the same

8:54

tool installation and we're using the same operating system now if you already have a lab environment set up

8:59

feel free to skip ahead to the section of this lesson where we install docker since you will need docker in order to

9:06

launch a vulnerable web application so let's start by creating a cali virtual machine with virtualbox now

9:12

don't worry this step is not difficult and it doesn't take too much time and again all of this is free if you don't already have virtualbox or

9:19

vmware go ahead and download whichever you'd prefer but i'll be using virtualbox all you have to do is go to virtualbox.org

9:26

and download the latest version for your current operating system right now i'm on a mac so i'll download

9:31

the os x version but if you're on windows you would download that version instead then you'll follow the steps to install

9:38

virtualbox at this point again if you have any issues during the installation and you can't figure out a solution please reach out

9:44

in the forums cyber dot com forward slash forums or on discord cyber.com forward slash discord

9:50

now once you have virtualbox installed and running it's time to set up kali linux i'll use an ova version this is a very

9:58

simple way of getting kali up and running without having to configure a lot of settings and it will work just fine for this

10:04

course so first we'll want to download cali at this url which is cali.org

10:10

slash downloads and since we're using the ova version and virtualbox we'll need to click on this link further

10:16

down on the page from there we'll download the 64-bit version made specifically for virtualbox

10:22

now this can take a few minutes all the way to a few hours depending on your internet connection

10:28

now while that's downloading a quick note for those on windows if you have wsl which stands for windows

10:34

subsystem for linux on windows 10 already installed and enabled

10:39

some students have reported issues with downloading and updating packages once inside of kali linux so the way to

10:45

fix that is to open up windows powershell with admin privileges and type in this command

10:51

of bcd edit set hypervisor launch type of off after that you'll

10:57

probably want to reboot just for good measure now you can always re-enable it by doing the opposite of this command

11:03

and setting it to on instead of off whenever you need and that should help prevent issues with

11:08

the labs in this course all right once you've downloaded the ova of cali go to virtualbox

11:14

and import the appliance by either going to file and then import appliance or by simply opening the ova

11:20

file then you'll want to start the import

11:26

process and this can take a few minutes after importing the appliance we can check the machine settings

11:32

and tweak anything that we'd like this is where you can add or remove resources to the virtual machine for example but

11:38

i'm personally going to leave it to the defaults so we're now ready to start the machine

11:45

[Music] we'll log in using kali and kali as the

11:51

username and password and we'll change this in just a moment then you'll probably need to resize the window since it's usually very small

11:58

when you first start it you can do that from the view menu

12:04

or you can do it by dragging the corner of the window now that we're logged in let's go ahead and change the default password we'll

12:11

type in passwd into the command line now at this point make sure that you read the instructions because

12:16

people oftentimes blow through these steps and they wonder why it doesn't work so the system will ask you to put in

12:23

your current password first which is going to be cali and then your new password twice so now

12:28

that we've got a new password let's go ahead and install docker we'll do sudo apt update just to get the latest packages

12:35

then we'll do sudo apt install y for yes docker dot io at this point

12:42

the docker system is started but not enabled if you want to enable docker to start automatically after a reboot which

12:48

won't be the case by default you can type in this command of systemctl enable docker dash

12:54

now now the last step is to add our non-root user to the docker group so that we can use docker without having

13:01

to type in sudo and at this point we're going to need to reload our settings so that this permissions change applies

13:07

the best way to reload permissions is to log out and back in again if you don't want to do that for

13:12

whatever reason there is a workaround that will temporarily help you which is to type in new group

13:18

docker but if that plus logging in and out doesn't work i would try to reboot the system

13:23

because otherwise you may find that other terminal windows haven't reloaded settings and so you may get a permission denied

13:30

error so with docker installed we can now pull in different environments as we need them without having to install any other

13:37

software for those environments it's all self-contained which is really practical for example if

13:42

we want to run the damn vulnerable webification or dvwa for short we can do that with this simple command

13:48

of docker run dash dash rm it dash p at port 8080 vulnerables

13:54

slash web dvwa if that doesn't work sometimes people say it doesn't work

13:59

try running this command first of docker pull vulnerables slash web dvwa and then rerun that same

14:06

docker run command that we just saw you will have to wait until it downloads the needed images and layers

14:12

and then starts the container and after that it will show you the apache access logs so you can see requests going through

14:19

the web server you can navigate to 127.0.0.1

14:24

in your browser in order to access the web application it will ask you to log in initially and

14:29

you can use the username admin and the password of password and then you will be redirected to a

14:35

setup.php page where you can check configurations and then create the database it should automatically redirect you to

14:42

login again but if it doesn't just scroll down to the bottom and click on log in in order to re-log in using admin and

14:49

password please note that you will have to do these quick steps every single time that you take down the environment and

14:55

bring it back up again so if you take a break from the course for example and then come back later after shutting down the

15:02

environment you will have to use the docker run command again so i would jot it down for easier access and if you want to

15:09

manually shut down the environment you can go back to the terminal window where we started the container and then

15:14

you can use ctrl c on windows or command c on mac in order to get your terminal window back as well as terminate the

15:21

docker environment so now that we've got our lab environment up and running it's time to download sql map by default sql map comes

## Downloading & installing the latest sqlmap

15:29

pre-installed with kali linux so as long as you followed the steps in the power lesson and downloaded the latest cali image

15:35

then you should have a very up-to-date sql map installation with that said it might not be the

15:40

latest version of sql map as you can see here our version of sql map is 1.4.11

15:47

stable the term stable can mean a few different things when it comes to software and it's up to

15:52

the developers to define its meaning but it typically means that the version has ironed out

15:57

most of the major bugs and issues and so it is the intended version to be used by most users so this is a

16:04

good version to use but again we can check and see if there are any newer versions by going to the project's github

16:10

repository and checking releases and we can see that there is a newer release version

16:15

which is a version bump to 1.5 so let's go ahead and download this version to our cali installation now to do that

16:22

we can use a few different methods including by downloading archives or by cloning

16:28

the git repository a benefit of cloning the repository is that you can easily update your

16:33

version by pulling changes through git at a later time versus if you downloaded an archive

16:38

that archive is quote-unquote stuck in time so you'd have to regularly go back and

16:44

download the latest archive in order to update that version so it's up to you but we do have git installed

16:49

on this machine so i'll go ahead and use that method as you can see though by the ending of our command

16:55

this should be treated as a dev meaning development version of sql map meaning that it's the

17:01

cutting edge and there may be some unknown or known bugs but it also means that you get the latest features so it

17:08

is a trade-off to be aware of and the version that you choose is ultimately up to you going into our

17:14

newly created directory with cd sqlmap dev we can run this version by typing python3

17:21

sqlmap.py for python now for this course we will stick to the default installation of sql map

17:27

to minimize the risk of bugs and issues along the way but feel free to try using this latest

17:32

version instead just be aware that there may be some slight differences so now that we've verified that we have a working

17:38

installation of sql map with two different versions and we've learned how to download the latest

17:43

version let's go ahead and complete this lesson and let's move on to the next where we will use sql map for the very first time whenever

## Using sqlmap for the first time

17:50

i download new command line tools there are a few things that i like to do number one i like to open up the tools

17:56

documentation which is usually on github at least for open source tools and then number two i like to use the dash

18:03

h option in my terminal dash h typically gives you a really good overview

18:08

of some of the most popular and useful commands or options that we can use with this particular tool

18:13

and it's usually in an easily digestible format which makes it easier to start with so combining that with the tools

18:19

documentation which can sometimes be hit or miss and we can be off to a really good start within a matter of minutes

18:25

luckily sql maps documentation is actually really really good for this type of open source project so

18:30

props go out to the authors bernardo and miroslav let's start by

18:36

looking over the introduction section of the tools documentation on github on this page they give us a very

18:42

simple example of what a target url might look like for sql injection here we have a target

18:48

ip address a path to an endpoint and a query parameter of id equals one so to check for sql

18:55

injection we might alter that parameter value from being just one to being something different like one

19:01

and one equals one or one and one equals two and then watching for the result

19:06

on the webpage or via the http request and this is something that we can do manually or that we can use a tool like

19:12

zap or burp to help with but of course this is also something that sql map can help with

19:18

by passing in that same target to sql map the tool will number one identify the

19:24

parameters to test so in this case the id number two identify which sql injection

19:29

techniques can be used to try and exploit that parameter number three fingerprint the backend database

19:35

management system to gather information about what technologies we're dealing with and then finally depending on what

19:41

it finds attempt to exploit vulnerabilities so that's great and that sounds awesome

19:47

but how do we go beyond concepts and actually put this into practice well we can head over to the usage

19:53

section of this documentation which includes a massive comprehensive list of all the sql map options

20:00

and how to use them but that can be overwhelming to beginners so instead let's go back to the command

20:06

line and let's use the dash h option to help us get started scrolling to the top right away we can

20:13

see a list of options for target as we saw with the prior example we want to be able to tell sql

20:19

map which target to go after and we can use the very first option of dash u or dash dash url in order to specify

20:26

that this is where we enter our url to go after which i will show in just a moment then

20:32

we see a section for request which lets us specify how we want sql map

20:37

to connect to our target url this is important to understand because different requests and targets

20:43

require different approaches if it's a get request that's different than if it's a post request

20:48

if it's a post request we'll need to pass in additional data with dash dash data equals all caps data

20:55

where that all caps data is a string to be sent through the post request we may also

21:00

need to pass in cookies with dash dash cookie equals cookie where the all caps is an http cookie

21:06

header value because if we're going after an application that requires authentication

21:12

and the authentication uses cookies for example then our sql map requests will not go through

21:17

and will get rejected by the application unless we also provide that cookie information another really

21:24

important set of options to know is under the enumeration category these options basically tell

21:30

sql map what to do and what to return if it finds a vulnerability that it can exploit

21:35

or if it finds information that is not properly concealed for example you can ask it to retrieve

21:41

the current database management system user with dash dash current user you can ask

21:46

for a list of the database tables with dash dash tables the entire database schema with

21:52

schema and a whole bunch more this is a really powerful set of options that we'll definitely use in this course

21:59

and that we use in the more advanced version of this course as well but finally before completing this

22:04

lesson and moving on to the next where we will issue our first command let's take a look at the wizard option

22:10

that's provided by sql map this option is really only useful when you're first getting started with sql

22:15

map because once you have more experience you can work faster and issue more powerful commands without

22:21

using the wizard but this option basically spoon feeds you what it needs to properly attack a target so let's try it even

22:28

though we don't have an environment running yet just to see what it does

22:33

so it will ask us to enter the full target url we'll just put localhost in here then

22:40

it asks for post data but if there's none you can just press enter which is the default then it will ask for injection

22:47

difficulty which changes both the level and risk options with a default of one

22:53

for normal so we'll just do that and then again it asks us for enumeration we'll choose just the basic enumeration

22:59

options at which point sql map will start to run but you'll see that it fails because again we have no environment running

23:06

on our localhost but we get to see how it asks us questions to determine what it is that we need

23:12

again this is just a simpler way of using the same options and commands that we would issue via the terminal

23:18

instead so feel free to use the wizard throughout the course if you'd like but we're basically not really going to

23:23

use it instead we're going to go straight into using the options that are available to us

23:29

but now that we've taken a look at the main available options to get us started in using sql map let's complete this lesson and let's

23:35

move on to the next where we will apply what we just learned in order to issue our first sql map

23:40

command and find as well as exploit our first sql injection with sql map so i'll see you there

## Finding our first SQL injection vulnerabilities with sqlmap

23:47

welcome back from what we talked about in the prior lesson we actually have enough to issue our first command against a

23:53

vulnerable target environment so let's see what we can do first things first let's turn on our

23:58

target environment if it's not already running just like we showed in a prior lesson on creating our lab environment

24:04

we'll do a docker run of the vulnerables web dvwa once that's up and running we

24:10

want to log into the application with admin password then we'll create the database and then we will re-log in

24:16

with the same credentials so now that our environment is on let's navigate to our target endpoint by

24:22

going to sql injections at this specific endpoint you can see that we have a user input field

24:28

asking for a user id and if we type in a random number and we click on submit you'll notice that the url changes

24:34

and this gives us a hint that it's probably a get request which we can confirm by opening up our

24:39

devtools using f12 on our keyboard and then going to the network tab and then resubmitting a

24:45

similar request then we can see that it is indeed a get request and when we click on it

24:50

we'll get additional information about that request let's leave this open for now and let's open up another terminal

24:57

to use sql map from as we know we need to pass in a target url with the dash u option so

25:03

let's copy this entire url from our browser into the terminal let's paste the url

25:09

including the parameter and let's paste it in our terminal in between quotes so we have sql map

25:16

dash u quote and then the target endpoint and then end quote now in some cases you may have to

25:22

specify a port in addition to the target url especially if it's not running on the default http

25:28

or https ports of 80 and 443 respectively because otherwise sql map will try to use those default

25:34

ports and it won't be able to establish a connection but in this case we are using port 80 and we specified

25:40

http so there's really no need to specify the port so let's submit this request now even though it won't work and we see that it

25:46

says that it's testing a connection to the target url but it got a 302 redirect to the

25:52

login.php page and it's asking us if we want to follow that redirect but we'll press ctrl or command c to get

25:59

out of that prompt now if you remember we had to log into the application in order to access this

26:04

endpoint so because we are behind a gated page we need to give sql maps the options it

26:09

needs to properly authenticate its calls and in the case of this application it's really easy because it just uses

26:15

two simple cookies so going back to our browser and the devtools we can see exactly what those cookies

26:21

are by clicking on the cookies tab and there we'll find the php session id cookie as well as

26:27

a security of low cookie so the php session id will look different on your machine than

26:33

it does on mine so don't copy and paste mine or that won't work instead you want to copy and

26:38

paste your own since we need to pass in cookie information to sql map let's go ahead and add that to our

26:44

command so we'll reuse the command we just issued prior to this but then we'll add dash dash cookie

26:49

equals and then in between quotes we'll put php sesh id exactly as it's spelt

26:55

and then we will copy and paste after an equal sign then we'll put a semicolon symbol and then we will do security

27:01

equals low and then again end quote so now that we've passed in the cookie sql map

27:06

knows how to authenticate against this application and the application will let sql map request through

27:13

so this should work now but let's add one more option to this command the dash dash tables this is an

27:19

enumeration option that we saw from the prior lesson that gives sql map a specific task to perform so in

27:26

this case the dash dash tables will make sql map enumerate the database tables

27:31

meaning that it will crawl the database structure and information to find and then return the names of all

27:37

the tables contained in this database so our final command looks like this we'll press enter and we'll let it do

27:44

its thing with how we entered this command sql map will periodically stop and prompt us to make a decision

27:50

here it's saying that the backend database management system is mysql so it's asking if it wants us to skip

27:56

test payloads specific to other database management systems which makes sense if we know it's my sequel there's really

28:03

no point in using unrelated payloads it will just add extra overhead and extra time

28:08

so we'll say yes which by the way you'll notice is the default because it's already capitalized

28:13

again it asks us another question do we want to include all the tests for mysql let's go ahead

28:19

and say yes which again is the default now as sql map is running let's go ahead and take a look at our terminal that's

28:25

running the container you'll notice requests coming through from sql map these are the requests that

28:30

are being sent by sql map to our server or in this case our local container so you can see the

28:36

exact payloads that are being submitted but once again it asks us a question and this can get really annoying especially

28:43

if you don't have any reason to doubt the defaults so i'll show you how to skip this next time that you run a sql map command

28:50

but for now let's go ahead and answer no with n since it says that the parameter id is vulnerable

28:55

so there's really no need for us to keep testing other parameters since there aren't any other parameters

29:00

anyway so scrolling back up to where we entered with no we can see additional information about

29:06

injection points successful payloads and then information that got returned

29:11

from sql map like the database management system which again was my sequel the database names of dvwa and

29:18

information schema as well as the tables in those databases so for the dwa you have two tables you

29:25

have guest book and then you have users and then the information schema which is critical to helping my sql

29:31

function has a bunch of other tables so this information is returned since we specifically asked

29:37

for database tables to be enumerated so sql map did exactly as we asked it attacked our target endpoint it found

29:44

a sql injection vulnerability via the id parameter it exploited that vulnerability and then it enumerated

29:51

the databases tables now before we complete this lesson let me show you a quick way to bypass those really annoying prompts

29:58

that have defaults using just a simple option this time let's also enumerate the database schema

30:04

instead of tables just to switch it up a little bit so we'll do dash dash schema and then we will add dash dash

30:10

batch adding that batch option is what's going to automatically use default behavior instead of asking for

30:17

user input so once we do that we can watch it do its thing and then it returns the results back containing the database schema so

30:25

this gives us a ton of information that will help us write a report back to our client about the vulnerability

30:31

the information disclosure and other potential exploitations if you've got experience with sql injections by hand

30:37

it's also quite impressive how it managed to do all of this automatically all it needed from us were a few options

30:43

and configurations and it handled the rest getting this much information and getting this far

30:49

would have taken far longer if we were to do it by hand so this is a really great start to using

30:54

sql map but there's still more that we can do so let's go ahead and complete this lesson and i'll see you in the next in the

## Extracting & cracking passwords from the database

31:00

previous lesson we successfully executed our first sql map command that found a sql injection vulnerability

31:06

and exploited it in order to give us all kinds of information about the applications database

31:11

including table names one of the tables that we saw was the users table which gives us a

31:17

pretty strong indication that there are probably user's passwords that are stored in there and as a red teamer

31:22

one of our goals might be to see whether we can get that password information and potentially also crack those

31:28

passwords so first things first we need to enumerate that table in order to see if we can grab those

31:33

passwords which will very likely be stored in an encrypted format and which we will have to also try and

31:39

crack and we can do this by narrowing down sql maps focus to enumerate columns

31:44

using dash dash columns in just this table with the dash t table option where the

31:50

table value represents the database table or tables to enumerate we'll also of course pass in the dash dash batch

31:58

in order to bypass the different prompts and then we'll press enter and then we'll be able to confirm that there is a passwords column

32:04

in this table so now we're going to use the dump option in order to dump the data that's

32:10

contained in the users table so we'll have dash dash dump dash t for

32:15

the table users and then dash dash batch so while this is working we can see that it asks us if we wanted

32:21

to store hashes to a temporary file for further processing with other tools while this is a really practical feature

32:28

we're not going to need that here next it asked if we wanted to crack the hashes via a dictionary based attack and the

32:35

default was yes which means that sql map then launches a dictionary based attack against the hashes that are found in the

32:41

database and we can see here that by default it uses the wordlist.txt file

32:47

that's included with kali linux but we could have specified a custom dictionary file or even a file

32:53

with a list of dictionary files then the final option that it asks is whether we want to use

32:58

common password suffixes but since it's much slower by default it says no then we can see

33:04

that it started the attack and shortly after it was able to crack multiple password hashes so now we end up with a neatly formatted

33:11

table that contains the user id the user the avatar and the password that includes both the

33:17

hash as well as the cracked passwords and then some other columns in this table so sql map not only enumerated the

33:24

table and found passwords but it also has a built-in cracker that was able to crack these password hashes

33:30

and give us the plain text version of that password pretty cool right now one more option that we can use that

33:36

does something kind of similar is the dash dash passwords option except this time it instructs sql map to both list and

33:44

crack the database management system users password hashes so not the applications users but the

33:51

user is in that database management system which means the users that manage the database when the user that we are

33:57

assuming by running these exploits against the database has read access to the system table that

34:03

contains the database management users passwords sql map is able to enumerate password hashes for each of those users

34:10

as we just saw earlier but unfortunately in this environment we do not have the proper permissions

34:15

so we just can't demonstrate this at this time but this is a good reminder that not everything will work every time

34:21

even if you're able to find a vulnerability that's why as a bug bounty hunter or pen tester or general security researcher you have

34:28

to continue pushing and trying to find different angles before giving up what might work in one environment or in

34:35

one circumstance may not work in other circumstances but the fact remains that these are built-in

34:40

features in sql map which makes it very practical because it avoids the need to download

34:45

or use other tools unless we're dealing with a more complex situation we're able to do quite a bit from just

34:52

this tool alone so now that we've completely compromised this application's user's passwords

34:57

let's mark this lesson as complete and let's move on to the next hey i hope you enjoyed this short course on sql map for beginners

## 36:16 - Next steps

35:03

this was meant to be an easy to follow introductory level course that taught you the basics of how to use

35:08

sql map in order to find sql injection vulnerabilities but as we talked about

35:14

sql map is a tool that's packed with amazing features and there's still much left to learn luckily you have all the tools and

35:20

information that you need to continue learning how to use sql map for free with just the github

35:26

documentation as well as the dash help option but if you want a guided course

35:31

that's similar to this one but that goes in a whole lot more detail and showcases more advanced features and functionality

35:38

in order to find more sql injections then check out my sql map deep dive course

35:43

on cyber.com and enroll today last but certainly not least i would love to hear

35:48

your feedback whether good or bad if there are any video lessons that you thought i could have improved on

35:53

i'd love to hear that please email me at kristoff cyber.com all right that's it from me i

36:00

hope to see you in my next course but even if you're not interested in that specific course i've got a bunch of other free courses

36:06

on cyber.com for you to check out thank you and i'll see you soon

36:15

you

All

From the series

From Cybr

Exploit

Computer Science

Presentations

For you
