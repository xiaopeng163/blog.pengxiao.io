title: Trace Packets in MPLS VPNv4 Network
date: 2012-10-07
categories:
- Cisco
- CCIE
tags:
- Cisco
- CCIE
- MPLS
- VPNv4
---

![](/thumbnails/vpnv4-packets/topo.png)

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/vpnv4-packets.md

主要想看看数据包是怎么在MPLS VPNv4 Core Network中路由传播的，顺便抓个两层标签的数据包。实验拓扑如上。

`AS 100`： `MPLS VPN`主干，通过`MP-BGP`传递`VPNv4`路由信息（其中`RR`反射`VPNv4`路由）。
`PE`和`CE`直接采用`EBGP`来承载客户路由。`CE`宣告路由`1.1.1.1/32`， `CE2`宣告`2.2.2.2/32`来模拟客户网络路由。相关配置见本文最后。
操作为在`CE1`上`ping 2.2.2.2` 到`CE2`，看数据包是如何传递，由于`CE1`到`PE1`，`PE2`到`CE2`是普通的`IPv4`，所以我们主要看去往`2.2.2.2`的数据包如何在`PE1，RR，PE2`直接传递的。


### 一，VPNv4路由收敛

从`PE1`的入方向看，`PE1`收到`RR`反射`PE2`过来的`VPNv4`路由`update`如下，其中`extended community`字段是`router target`信息，标识导入相关的`VPN`路由表，`VPN`的`NLRI`是真正的`VPN`路由，
其中`AFI`和`SAFI`代表了`VPNv4`；`Nexthop`属性是此`VPN`路由的下一跳（里面默认加了`RD 0:0`，正是为了和VPN路由对应一致`RD+IP prefix`）;

最下面是真正的`VPNv4`路由：

    RD=65002:1
    Label Stack = 19 (第二标签的由来)
    prefix = 2.2.2.2/32

![](/thumbnails/vpnv4-packets/wireshark1.png)

### 二，ping 2.2.2.2

#### PE1：

（1）收到从`CE1`过来的去往`2.2.2.2`的数据包，`PE1`会查看自己的VPN（对应此CE）路由表，得到下一跳是`192.168.1.3`，并且得到VPN的出站标签是`19`

![](/thumbnails/vpnv4-packets/show1.png)


（2）查看本地MPLS转发表，得知下一跳`192.168.1.3`出站标签`17`

![](/thumbnails/vpnv4-packets/show2.png)

（3）打上以上两个MPLS标签，成ICMP报文发给`RR 10.12.1.2`

![](/thumbnails/vpnv4-packets/wireshark2.png)

#### RR：

RR收到PE1发过来的ICMP数据包，直接根据MPLS转发表找到`192.168.1.3`

![](/thumbnails/vpnv4-packets/show3.png)

出站`pop tag`（此处应该是MPLS的PHP技术），POP而不是untag，说明RR发现此MPLS数据包里标签并非栈低。
成ICMP报文发给下一跳`10.23.1.2`，也就是`PE2`

![](/thumbnails/vpnv4-packets/wireshark3.png)

#### PE2：

PE2从RR收到的ICMP报文，其实还有一层标签。然后就去标签直接发往相应的下一跳了。

![](/thumbnails/vpnv4-packets/show4.png)

数据包的返回的过程类似。


### 附：

CE1：

```
router bgp 65001
no synchronization
bgp log-neighbor-changes
network 1.1.1.1 mask 255.255.255.255
neighbor 12.0.0.2 remote-as 100
neighbor 12.0.0.2 ebgp-multihop 255
no auto-summary
!
```
PE1：

```
router bgp 100
bgp router-id 192.168.1.1
no bgp default ipv4-unicast
bgp log-neighbor-changes
neighbor 192.168.1.2 remote-as 100
neighbor 192.168.1.2 update-source Loopback0
!
address-family ipv4
  neighbor 192.168.1.2 activate
  no auto-summary
  no synchronization
exit-address-family
!
address-family vpnv4
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 send-community extended
exit-address-family
!
address-family ipv4 vrf cisco
  redistribute connected
  neighbor 12.0.0.1 remote-as 65001
  neighbor 12.0.0.1 activate
  no synchronization
exit-address-family
!
```
RR：

```
router bgp 100
bgp router-id 192.168.1.2
no bgp default ipv4-unicast
no bgp default route-target filter
bgp log-neighbor-changes
neighbor 192.168.1.1 remote-as 100
neighbor 192.168.1.1 update-source Loopback0
neighbor 192.168.1.3 remote-as 100
neighbor 192.168.1.3 update-source Loopback0
!
address-family vpnv4
  neighbor 192.168.1.1 activate
  neighbor 192.168.1.1 send-community both
  neighbor 192.168.1.1 route-reflector-client
  neighbor 192.168.1.3 activate
  neighbor 192.168.1.3 send-community extended
  neighbor 192.168.1.3 route-reflector-client
exit-address-family
!
```

PE2：

```
router bgp 100
bgp router-id 192.168.1.3
no bgp default ipv4-unicast
bgp log-neighbor-changes
neighbor 192.168.1.2 remote-as 100
neighbor 192.168.1.2 update-source Loopback0
!
address-family ipv4
  neighbor 192.168.1.2 activate
  no auto-summary
  no synchronization
exit-address-family
!
address-family vpnv4
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 send-community both
exit-address-family
!
address-family ipv4 vrf cisco
  redistribute connected
  neighbor 23.0.0.2 remote-as 65002
  neighbor 23.0.0.2 activate
  no synchronization
exit-address-family
```

CE2：

```
router bgp 65002
no synchronization
bgp log-neighbor-changes
network 2.2.2.2 mask 255.255.255.255
neighbor 23.0.0.1 remote-as 100
neighbor 23.0.0.1 ebgp-multihop 255
no auto-summary
!
```