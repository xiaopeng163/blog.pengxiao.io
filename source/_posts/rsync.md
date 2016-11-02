title: Rsync Usage
date: 2016-11-02
categories:
- Linux
tags:
- rsync
---


## Install

Rsync is installed in many linux systems by default.

```bash
rsync --version
rsync  version 3.0.9  protocol version 30
Copyright (C) 1996-2011 by Andrew Tridgell, Wayne Davison, and others.
Web site: http://rsync.samba.org/
Capabilities:
    64-bit files, 64-bit inums, 64-bit timestamps, 64-bit long ints,
    socketpairs, hardlinks, symlinks, IPv6, batchfiles, inplace,
    append, ACLs, xattrs, iconv, symtimes

rsync comes with ABSOLUTELY NO WARRANTY.  This is free software, and you
are welcome to redistribute it under certain conditions.  See the GNU
General Public Licence for details.
```

## Usage Example

We want to sync folders from one host to another host, so there are a source host and a target host.

### Target host

In target host we will start a rsync daemon process, it will listen on port 873 by default, and wait connection from the source host.
Once the connection is established, the source host will sync the folder/file to the target host.

```bash
/usr/bin/rsync --daemon --config=/etc/rsyncd/rsyncd.conf
```

Before that we should prepare some files located under `/etc/rsyncd/`

```bash
$ pwd
/etc/rsyncd
$ ls -l
total 48
-rw-r--r-- 1 root root   664 Nov  2 13:41 rsyncd.conf
-rw------- 1 root root    17 Nov  2 13:41 rsyncd.password
```
For `rsyncd.password`, please make sure the read/write access only for the current user, and it contains the SSH user/password which will be used by
the source host.

```bash
$ more rsyncd.password
demo:demo
```
For `rsyncd.conf`

```bash
$ more rsyncd.conf
uid = root
gid = root
user chroot = no
max connections = 50
timeout = 180
pid file = /etc/rsyncd/rsyncd.pid
lock file= /etc/rsyncd/rsyncd.lock
log file = /etc/rsyncd/rsyncd.log
user chroot = no
max connections = 50
timeout = 180

transfer logging = yes
log format = %t %a %m %f %b
syslog facility = local3
secrets file = /etc/rsyncd/rsyncd.password

[target_folder]
path=/home/demo/target_folder/
ignore errors
hosts allow =1.1.1.1
hosts deny = *
secrets file = /etc/rsyncd/rsyncd.password
read only = no
list = no
auth users = demo
```

`hosts allow` is the source host ip address


### Source host

Prepare two files:

```bash
$ ls -l
total 8
-rwxr-xr-x. 1 root root 175 Nov  2 13:51 rsync.sh
-rw-------. 1 root root   9 Nov  2 13:47 rsyncpass
```

For the shell script:

```bash
$ more rsync.sh
#!/bin/bash
src=/home/demo/source_folder/
user=demo
ip=2.2.2.2
/usr/bin/rsync -zr --append-verify --delete --password-file=/home/demo/rsyncpass $src $user@$ip::rib
#
```

For the `rsyncpass`, please make sure the read/write access only for the current user. It only contains the password.

```bash
$ more rsyncpass
demo
```

Now we can put the `rsync.sh` to the crontab, done!

```bash
$ crontab -l
* * * * * //home/demo/rsync.sh
```

You can check the results from the target host in the target folder.
