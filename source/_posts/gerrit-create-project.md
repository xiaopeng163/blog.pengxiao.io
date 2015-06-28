title: Gerrit Create Project
date: 2013-12-10
categories:
- Git
tags:
- Git
---

前些天把gerrit装起来了，一直没试。参考 https://gerrit-documentation.storage.googleapis.com/Documentation/2.7/project-setup.html
刚试了试创建project，方法基本上有两三种：
使用ssh key，这和github，gitlab，以及gitolite的权限管理很相似，非常安全和方便。

1. 直接在gerrit的web上创建，这个最简单

![](/thumbnails/gerrit-creat-project/1.png)

2. 手工创建代码库然后添加到 gerrit

(1) create repository
    
    git --git-dir=$base_path/test.git init
    
(2) register project

    ssh -p 29418 your-name@localhost gerrit flush-caches --cache project
    
这个命令能成功运行的前提是，需要把gerrit的pub-key添加到 gerrit web你的个人用户中。
在setting，add ssh-key。 如果没有添加，会有权限报错

3. 远程SSH 添加

   同样，必须确保主机的pub-key add到了gerrit web你个人setting中， 然后运行
   
```
   ssh -p 29418 your-name@gerrit-ip-address gerrit create-project --name test
```

关于如何生成key，以及如何在.ssh/config里增加配置方便使用，请参考相关资料。

今天先玩到这。