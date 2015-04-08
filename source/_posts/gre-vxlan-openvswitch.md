title: GRE and VXLAN with Open vSwitch
date: 2014-10-07
categories:
- OpenStack
tags:
- Linux
- OpenStack
---


![](/thumbnails/gre-vxlan-openvswitch/1.png)

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/_posts/gre-vxlan-openvswitch.md

试了下OVS的一些隧道封装功能（GRE，VXLAN）。

实验:实现两个虚机之间的Network namespace之间通信。如下图所示：


#### 配置host 1
在host1中创建一个network namespace `red`，以及一对veth（veth0，veth1）
，其中veth0添加到`red`里，配置IP并且up起来。

``` bash
$ sudo ip netns add red
$ sudo ip link add veth0 type veth peer name veth1
$ sudo ip link set veth0 netns red
$ sudo ip netns exec red ip addr add 10.1.1.1/24 dev veth0
$ sudo ip netns exec red ip a
7: lo: <LOOPBACK> mtu 16436 qdisc noop state DOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
9: veth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether ca:4f:8c:a1:3b:24 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.1/24 scope global veth0
    inet6 fe80::c84f:8cff:fea1:3b24/64 scope link 
       valid_lft forever preferred_lft forever
```

OVS创建一个bridge `br-int`，并且把前面创建的veth对里的`veth1`作为端口添加到这个bridge上并up起来，然后给端口打上vlan tag 10。
``` bash
$ sudo ovs-vsctl add-br br-int
$ sudo ovs-vsctl add-port br-int veth1
$ sudo ip link set veth1 up
$ sudo  ovs-vsctl set port veth1 tag=10
```

#### 配置host2

host2的配置和host1几乎完全相同，唯一不同的是veth0的IP地址时10.1.1.2/24

``` bash
$ sudo ip netns exec red ip a
9: lo: <LOOPBACK> mtu 16436 qdisc noop state DOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
11: veth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 12:09:83:aa:97:57 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.2/24 scope global veth0
    inet6 fe80::1009:83ff:feaa:9757/64 scope link 
       valid_lft forever preferred_lft forever
```
#### 配置host1和host2之间的GRE隧道

在host1上配置
``` bash
$ sudo ovs-vsctl add-port br-int gre0 -- set interface gre0 type=gre options:remote_ip=192.168.4.202
```
其中192.168.4.202是host2上的eth2

在host2上配置
``` bash
$ sudo ovs-vsctl add-port br-int gre0 -- set interface gre0 type=gre options:remote_ip=192.168.4.201
```
其中192.168.4.201是host1的eth2地址。

可以通过OVS查看配置

``` bash
$ sudo ovs-vsctl show
d33f919d-82b7-4541-a4c2-39ca45286a83
    Bridge br-int
        Port "veth1"
            tag: 10
            Interface "veth1"
        Port br-int
            Interface br-int
                type: internal
        Port "gre0"
            Interface "gre0"
                type: gre
                options: {remote_ip="192.168.4.201"}
    ovs_version: "1.11.0"
```

验证两个host上的red network namespace是否连通

在host1上ping

```
$ ip netns exec red ping -c 4 10.1.1.2
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=0.977 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.915 ms
64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=1.00 ms
64 bytes from 10.1.1.2: icmp_seq=4 ttl=64 time=0.658 ms
--- 10.1.1.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 0.658/0.889/1.008/0.140 ms
```

同时在host2的eth2上抓包可以看到

``` bash
$ sudo tcpdump -i eth2
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth2, link-type EN10MB (Ethernet), capture size 65535 bytes
16:23:59.597256 IP 192.168.4.201 > 192.168.4.202: GREv0, length 106: IP 10.1.1.1 > 10.1.1.2: ICMP echo request, id 13321, seq 1, length 64
16:23:59.597499 IP 192.168.4.202 > 192.168.4.201: GREv0, length 106: IP 10.1.1.2 > 10.1.1.1: ICMP echo reply, id 13321, seq 1, length 64
16:24:00.598288 IP 192.168.4.201 > 192.168.4.202: GREv0, length 106: IP 10.1.1.1 > 10.1.1.2: ICMP echo request, id 13321, seq 2, length 64
16:24:00.598461 IP 192.168.4.202 > 192.168.4.201: GREv0, length 106: IP 10.1.1.2 > 10.1.1.1: ICMP echo reply, id 13321, seq 2, length 64
16:24:01.600200 IP 192.168.4.201 > 192.168.4.202: GREv0, length 106: IP 10.1.1.1 > 10.1.1.2: ICMP echo request, id 13321, seq 3, length 64
16:24:01.600441 IP 192.168.4.202 > 192.168.4.201: GREv0, length 106: IP 10.1.1.2 > 10.1.1.1: ICMP echo reply, id 13321, seq 3, length 64
16:24:02.133695 IP 192.168.4.1.17500 > 192.168.4.255.17500: UDP, length 104
16:24:02.601673 IP 192.168.4.201 > 192.168.4.202: GREv0, length 106: IP 10.1.1.1 > 10.1.1.2: ICMP echo request, id 13321, seq 4, length 64
16:24:02.601833 IP 192.168.4.202 > 192.168.4.201: GREv0, length 106: IP 10.1.1.2 > 10.1.1.1: ICMP echo reply, id 13321, seq 4, length 64
```
可以看到有ICMP的包通过GRE的封装发送过来。


#### 配置host1和host2之间VXLAN封装

首先在host1和host2上把ovs上的gre0 port 删掉

``` bash
$ sudo  ovs-vsctl del-port gre0
```
然后在host1和host2上分别配置vxlan，和GRE的区别就是type字段改成`vxlan`

``` bash
$ sudo ovs-vsctl add-port br-int vxlan0 -- set interface vxlan0 type=vxlan options:remote_ip=192.168.4.201
$ sudo ovs-vsctl show
d33f919d-82b7-4541-a4c2-39ca45286a83
    Bridge br-int
        Port "veth1"
            tag: 10
            Interface "veth1"
        Port br-int
            Interface br-int
                type: internal
        Port "vxlan0"
            Interface "vxlan0"
                type: vxlan
                options: {remote_ip="192.168.4.201"}
    ovs_version: "1.11.0"
```

验证
``` bash
tcpdump -i eth2
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth2, link-type EN10MB (Ethernet), capture size 65535 bytes
16:38:40.018375 ARP, Request who-has 192.168.4.202 tell 192.168.4.201, length 46
16:38:40.018422 ARP, Reply 192.168.4.202 is-at 08:00:27:fd:39:c8 (oui Unknown), length 28
16:38:40.018724 IP 192.168.4.201.60827 > 192.168.4.202.4789: UDP, length 54
16:38:40.019324 IP 192.168.4.202.56167 > 192.168.4.201.4789: UDP, length 54
16:38:40.020405 IP 192.168.4.201.57614 > 192.168.4.202.4789: UDP, length 110
16:38:40.021146 IP 192.168.4.202.39657 > 192.168.4.201.4789: UDP, length 110
16:38:41.018502 IP 192.168.4.201.57614 > 192.168.4.202.4789: UDP, length 110
16:38:41.018674 IP 192.168.4.202.39657 > 192.168.4.201.4789: UDP, length 110
16:38:42.019908 IP 192.168.4.201.57614 > 192.168.4.202.4789: UDP, length 110
16:38:42.020198 IP 192.168.4.202.39657 > 192.168.4.201.4789: UDP, length 110
16:38:43.021400 IP 192.168.4.201.57614 > 192.168.4.202.4789: UDP, length 110
16:38:43.021597 IP 192.168.4.202.39657 > 192.168.4.201.4789: UDP, length 110
16:38:45.018441 ARP, Request who-has 192.168.4.201 tell 192.168.4.202, length 28
16:38:45.019091 ARP, Reply 192.168.4.201 is-at 08:00:27:f4:b2:82 (oui Unknown), length 46
16:38:45.020773 IP 192.168.4.202.56167 > 192.168.4.201.4789: UDP, length 54
16:38:45.021771 IP 192.168.4.201.55994 > 192.168.4.202.4789: UDP, length 54
```

端口4789就是vxlan的端口。

参考：

[http://blog.scottlowe.org/2013/09/09/namespaces-vlans-open-vswitch-and-gre-tunnels/](http://blog.scottlowe.org/2013/09/09/namespaces-vlans-open-vswitch-and-gre-tunnels/)

[http://blog.scottlowe.org/2013/05/07/using-gre-tunnels-with-open-vswitch/](http://blog.scottlowe.org/2013/05/07/using-gre-tunnels-with-open-vswitch/)

[http://blog.csdn.net/landhang/article/details/8885927](http://blog.csdn.net/landhang/article/details/8885927)
