title: BGP Outbound Route Filtering
date: 2012-10-21
categories:
- Cisco
- CCIE
tags:
- Cisco
- CCIE
- BGP
---

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/bgp-outbound-route-filtering.md

Reference RFC 5291

http://blog.ine.com/2008/05/05/understanding-bgp-outbound-route-filtering-bgp-orf/ 说的很明白。

主要是想看一看这个新的BGP capability

搭了一个简单的拓扑：其中CE模拟客户路由器，PE上以回环口模拟路由发往CE，PE和CE之间实现ORF

![](/thumbnails/bgp-outbound-route-filtering/1.png)

配置命令其实很简单。

```
PE#sh run | b r b
router bgp 100
 no synchronization
 bgp log-neighbor-changes
 network 192.168.1.0
 network 192.168.2.0
 network 192.168.3.0
 network 192.168.4.0
 neighbor 10.0.0.2 remote-as 200
 neighbor 10.0.0.2 capability orf prefix-list receive
 no auto-summary
!

CE#sh run | b r b
router bgp 200
 no synchronization
 bgp log-neighbor-changes
 neighbor 10.0.0.1 remote-as 100
 neighbor 10.0.0.1 capability orf prefix-list send
 neighbor 10.0.0.1 soft-reconfiguration inbound
 neighbor 10.0.0.1 prefix-list AS_100_IN in
 no auto-summary
!
ip prefix-list AS_100_IN seq 1 permit 192.168.1.0/24
ip prefix-list AS_100_IN seq 5 permit 192.168.2.0/24
```
 

#### （1） open message进行capabilities协商

CE发给PE：

![](/thumbnails/bgp-outbound-route-filtering/2.jpg)

PE发给CE：

![](/thumbnails/bgp-outbound-route-filtering/3.jpg)

（2）通过route refresh message来携带ORF的entry。

RFC中规定的格式为：

下面是携带了ORF的BGP route refresh message：

![](/thumbnails/bgp-outbound-route-filtering/4.png)

下面是具体一个entry的格式：

![](/thumbnails/bgp-outbound-route-filtering/5.png)

可以看到第一个字节分3部分：

2bit action： 0 for ADD，1 for REMOVE，2 for REMOVE-ALL

1 bit match: 0 for PERMET, 1 for DENY

5 bit 保留位

下面是Cisco ORF的包结构

![](/thumbnails/bgp-outbound-route-filtering/6.jpg)

最后是由此ORF产生的Update结果

![](/thumbnails/bgp-outbound-route-filtering/7.jpg)

具体的Operation还是要参考RFC5291