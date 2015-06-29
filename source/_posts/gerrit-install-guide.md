title: Gerrit install Guide
date: 2013-12-02
categories:
- Git
tags:
- Git
---

### 1. Prepare

(1) Linux operation system；

(2) Install mysql, create user and table;

```
 mysql CREATE USER 'gerrit2'@'localhost' IDENTIFIED BY 'secret';
 CREATE DATABASE reviewdb;
 ALTER DATABASE reviewdb charset=latin1;
 GRANT ALL ON reviewdb.* TO 'gerrit2'@'localhost'; FLUSH PRIVILEGES
```

Please do not set `charset` to utf8!

(3) create user: `useradd gerrit2`

### 2. Install

(1) Create a new folder， `/home/gerrit2/gerrit/`

(2) Download gerrit https://code.google.com/p/gerrit/

(3) run

```
java -jar gerrit.war init -d /home/gerrit2/gerrit
```

There are some prompts ，all default is ok，after finished that, you can edit `/home/gerrit2/gerrit/etc/gerrit.config` to
change some options, below is a `gerrit.config` example:

```
[gerrit]
        basePath = git
        canonicalWebUrl = http://127.0.0.1:8080/
[database]
        type = mysql
        hostname = localhost
        database = reviewdb
        username = gerrit2
[auth]
        type = LDAP
[ldap]
        server = ldap://ldap.example.com
        accountBase = ou=active,ou=a,ou=b,o=example.com
        accountPattern = (&(objectClass=person)(uid=${username}))
        accountSshUsername = ${uid.toLowerCase}
        accountFullName = ${cn}
        accountEmailAddress = mail
        groupBase = ou=active,ou=a,ou=b,o=example.com
[sendemail]
        smtpServer = smtp.test.com
        smtpUser = gerrit2
[container]
        user = gerrit2
        javaHome = /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.9.x86_64/jre
[sshd]
        listenAddress = *:29418
[httpd]
        listenUrl = http://*:8080/
[cache]
        directory = cache
```

run `bin/gerrit.sh start` to start，if any errors, please see log files in `logs/`.

open the browser:  http://127.0.0.1:8080