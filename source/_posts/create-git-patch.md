title: How to Create a Git Patch
date: 2014-09-07
categories:
- Git
tags:
- Git
- Linux
---

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/create-git-patch.md

#### Clone repository
``` bash
xiaopeng@cisco ~ $ git clone https://github.com/xiaopeng163/twisted-fsm
Cloning into 'twisted-fsm'...
remote: Counting objects: 17, done.
remote: Total 17 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (17/17), done.
Checking connectivity... done.
```
#### Create local branch
```bash
xiaopeng@cisco ~ $ cd twisted-fsm
xiaopeng@cisco ~/twisted-fsm $ git branch 
* master
xiaopeng@cisco ~/twisted-fsm $ git branch feature/add-more-log
xiaopeng@cisco ~/twisted-fsm $ git branch 
  feature/add-more-log
* master
xiaopeng@cisco ~/twisted-fsm $ git checkout feature/add-more-log 
Switched to branch 'feature/add-more-log'
xiaopeng@cisco ~/twisted-fsm $
```
#### Change and commit
``` bash
xiaopeng@cisco ~/twisted-fsm $ git commit -s 
[feature/add-more-log aa50dfb] The headline of your commit
 1 file changed, 1 insertion(+)

```
`-s` means Signed-off-by

``` bash
xiaopeng@cisco ~/twisted-fsm $ git log 
commit aa50dfb2b15981d673f875f77dd5bf28dfa1def6
Author: Peng Xiao <your-id@gmail.com>
Date:   Fri Sep 5 15:08:09 2014 +0800

    The headline of your commit
    
    The detail description of your commit
     - xxxxx
     - xxxxx
    
    Signed-off-by: Peng Xiao <your-id@gmail.com>
```
#### Generate patch

```
xiaopeng@cisco ~/twisted-fsm $ git format-patch -M master 
0001-The-headline-of-your-commit.patch
xiaopeng@cisco ~/twisted-fsm $ more 
0001-The-headline-of-your-commit.patch
From aa50dfb2b15981d673f875f77dd5bf28dfa1def6 Mon Sep 
17 
00:00:00 
2001From:
Peng Xiao Date: Fri, 
5 
Sep 
2014 
15:08:09 
+0800Subject:
[PATCH] The headline of your commit The detail description of your
commit - xxxxx - xxxxx Signed-off-by: Peng Xiao --- protocol.py
| 
1 
+ 
1 
file changed, 
1 
insertion(+) diff --git a/protocol.py b/protocol.py index 
8705331..eb3f248 
100644---
a/protocol.py +++ b/protocol.py @@ -48,4 
+48,5 
@@ class DemoProtocol(Protocol):
#TODO: to do some process with data received from TCP buffer
self.buffer += data + LOG.debug("Receive
data from %s, data length is %s."
% (self.transport.getPeer().host, len(data)) pass --
1.9.1
```
#### Send the patch through email

edit `.gitconfig` file

``` bash
[imap]
  folder = "[Gmail]/Drafts"
  host = imaps://imap.gmail.com
  user = user@gmail.com
  pass = p4ssw0rd
  port = 993
  sslverify = false
```
through send mail
``` bash
xiaopeng@cisco ~/twisted-fsm $ git send-email *.patch
```
