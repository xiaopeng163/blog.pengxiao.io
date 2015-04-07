title: Linux Network Namespace
date: 2014-10-05
categories:
- Linux
tags:
- Linux
- OpenStack
---

Network Namespace可以实现网络的隔离，有点像路由器里的VRF。在虚拟化和LXC中有很重要的用处。

#### 创建Network Namespace

``` bash
ip netns add <new namespace name>
```

例如：
``` bash
ip netns add test
```

查看namespace

``` bash
ip netns list
```

#### 给Namespace添加接口

创建的Namespace不能添加真实的物理接口，只能添加虚拟接口`veth`（virtual Ethernet interface），它们经常成对出现并且像一个管道一样连在一起。

创建一对veth：`veth0`和`veth1`
``` bash
ip link add veth0 type veth peer name veth1
```

通过命令可以查看我们创建的veth
``` bash
[root@controller0 ~]# ip link list
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether 08:00:27:ec:3c:70 brd ff:ff:ff:ff:ff:ff
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether 08:00:27:d1:f2:b3 brd ff:ff:ff:ff:ff:ff
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether 08:00:27:ad:03:e8 brd ff:ff:ff:ff:ff:ff
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:b2:eb:13 brd ff:ff:ff:ff:ff:ff
6: virbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
    link/ether 52:54:00:eb:0e:7e brd ff:ff:ff:ff:ff:ff
7: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 500
    link/ether 52:54:00:eb:0e:7e brd ff:ff:ff:ff:ff:ff
10: veth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 86:e4:2c:b1:77:d0 brd ff:ff:ff:ff:ff:ff
11: veth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 82:bf:54:c0:5c:a9 brd ff:ff:ff:ff:ff:ff
```
现在这两个veth都是属于默认（global）的Network  Namespace，下面我们把veth0放到test的namespace里，veth1保留在global的namespace里。

``` bash
[root@controller0 ~]# ip link set veth0 netns test
[root@controller0 ~]# ip netns exec test ip a
9: lo: <LOOPBACK> mtu 16436 qdisc noop state DOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
11: veth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 82:bf:54:c0:5c:a9 brd ff:ff:ff:ff:ff:ff
```
发现veth0已经跑到test这个namespace里了，全局的network namespace里已没有了veth0.

目前veth0和veth1时down的状态，下面我们为两个veth对配置IP地址

``` bash
ip netns exec test ip addr add 192.168.10.2/24 dev veth0 
ip netns exec test ip link set veth0 up
[root@controller0 ~]# ip netns exec test ip a
9: lo: <LOOPBACK> mtu 16436 qdisc noop state DOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
11: veth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN qlen 1000
    link/ether 82:bf:54:c0:5c:a9 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.2/24 scope global veth0
[root@controller0 ~]#
```
给veth1配置IP地址，veth1在global的Network Namespace里
``` bash
ip addr add 192.168.10.1/24 dev veth1 up
[root@controller0 ~]# ip a
10: veth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 86:e4:2c:b1:77:d0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.1/24 scope global veth1
    inet6 fe80::84e4:2cff:feb1:77d0/64 scope link 
       valid_lft forever preferred_lft forever
[root@controller0 ~]# ip netns exec test ip a
9: lo: <LOOPBACK> mtu 16436 qdisc noop state DOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
11: veth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 82:bf:54:c0:5c:a9 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.2/24 scope global veth0
    inet6 fe80::80bf:54ff:fec0:5ca9/64 scope link 
       valid_lft forever preferred_lft forever
```
可以看到veth0和veth1都up了起来。验证一下连通性。

``` bash
[root@controller0 ~]# ping 192.168.10.2
PING 192.168.10.2 (192.168.10.2) 56(84) bytes of data.
64 bytes from 192.168.10.2: icmp_seq=1 ttl=64 time=0.084 ms
64 bytes from 192.168.10.2: icmp_seq=2 ttl=64 time=0.102 ms
^C
--- 192.168.10.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1326ms
rtt min/avg/max/mdev = 0.084/0.093/0.102/0.009 ms
[root@controller0 ~]# ip netns exec test ping 192.168.10.1
PING 192.168.10.1 (192.168.10.1) 56(84) bytes of data.
64 bytes from 192.168.10.1: icmp_seq=1 ttl=64 time=0.076 ms
64 bytes from 192.168.10.1: icmp_seq=2 ttl=64 time=0.076 ms
^C
--- 192.168.10.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1552ms
rtt min/avg/max/mdev = 0.076/0.076/0.076/0.000 ms
[root@controller0 ~]# 
```

从外往里ping和从里往外ping都是通的。

