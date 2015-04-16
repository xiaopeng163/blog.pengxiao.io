title: IP Multicast: GNS3+VLC模拟组播视频应用
date: 2012-11-26
categories:
- Cisco
- CCIE
tags:
- Cisco
- CCIE
- Multicast
---

source https://github.com/xiaopeng163/www.pythoner.io/blob/master/source/ip-multicast-gns-vlc.md

GNS3提供的这个组播实验是非常好的http://www.anandnetwork.com/2011/11/multicast-testing-with-vlc-media-player.html。前些日子看了些组播的东西，虽然都会详细的去讲IGMP,PIM等，但没有source和receiver，不能称其为一个完整的组播系统，因而就很难去实际分析组播数据流到底是怎么在组播系统中转发的，而source和receiver是实际应用，不管是GNS3或者是IOU、IOL都模拟不了。而上面那个实验，解决了这个问题。
 
唯一不足之处：发送的是视频流太大，抓包太多，不便于分析。其实我只想让source每隔T秒重复发送一个单词Cisco，然后能在多个receiver上收到即可，这个后面可以想想办法。说不定用Python就可以实现，那就不用VLC了http://chaos.weblogs.us/archives/164
 
实验搭建很简单，拓扑只选了一台组播路由器，一个source和receiver。想要更好的理解PIM的各种模式，后面可能还得多加几台路由器，多加几个receiver去分析。

![](/thumbnails/ip-multicast-gns-vlc/1.png)

先说下基本配置
C1是本地的一个adaper，IP 192.168.1.2/24 Gateway 192.168.1.1/24
Qemu1 是起了一台非常非常mini的linux虚机 IP 192.168.100.1/24 Gateway 192.168.100.2/24
上面跑一个组播源，使用VLC循环播放一段Pixar的动画。
R1的基本配置
```
ip multicast-routing
interface Ethernet1/0
 ip address 192.168.1.1 255.255.255.0
 ip pim dense-mode
 ip igmp static-group 224.1.1.1
 duplex half
!        
interface Ethernet1/1
 ip address 192.168.100.2 255.255.255.0
 ip pim dense-mode
 ip igmp static-group 224.1.1.1
 duplex half
```
暂且使用PIM密集模式
 
（1）实验开始

在虚机上运行里面的VLC脚本启动

![](/thumbnails/ip-multicast-gns-vlc/2.jpg)

(2) 在本机上开启VLC，输入组播地址，点play

![](/thumbnails/ip-multicast-gns-vlc/3.png)

就可以看到视频了

![](/thumbnails/ip-multicast-gns-vlc/4.png)
 
有了这个东西，通过复杂化中间的组播路由器拓扑，就可以更好的学习组播的东西了。