---
layout:     post
title:      "计算机网络"
subtitle:   "第4章 网络层"
date:       2017-06-09 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 计算机网络
    - 基础知识
---

### 网络层提供的服务

虚电路服务：进行通信时应当先建立连接，以保证双方通信所需的一切网络资源

数据报服务：只提供简单灵活的、无连接的、尽最大努力交付的数据报服务（所传送的分组可能出错、丢失、重复和失序，也不保证分组交付的时限）

![网络层提供的服务](/img/computer-network/network-layer-serve.png)

虚电路服务与数据报服务的对比：

|对比的方面|虚电路服务|数据报服务|
|:----------:|:----------:|:----------:|
|**思路**|可靠通信应当由网络来保证|可靠通信应当由用户主机来保证|
|**连接的建立**|必须有|不需要|
|**分组的转发**|属于同一条虚电路的分组均按照统一路由进行转发|每个分组独立选择路由进行转发|
|**当结点出故障时**|所有通过出故障的结点的虚电路均不能工作|出故障的结点可能会丢失分组，一些路由可能会发生变化|
|**分组的顺序**|总是按发送顺序到达终点|到达终点时不一定按发送顺序|
|**端到端的差错处理和流量控制**|可以由网络负责，也可以由用户主机负责|由用户主机负责|

TCP/IP体系的网络层提供的是数据报服务

### 网际协议IP

##### 分类IP地址

![分类的IP地址](/img/computer-network/IP-address.png)

![IP地址的范围](/img/computer-network/IP-address-range.png)

![特殊IP地址](/img/computer-network/IP-address-special.png)

用转发器或网桥连接起来的若干个局域网仍为一个网络

##### IP地址与硬件地址

|协议栈|地址|
|:----------:|:----------:|
|应用层|IP地址（逻辑地址）|
|运输层|IP地址（逻辑地址）|
|网络层|IP地址（逻辑地址）|
|数据链路层|物理地址|
|物理层|物理地址|

![IP地址与硬件地址的区别](/img/computer-network/IP-hardware-address.png)

IP地址放在IP数据报的首部，硬件地址放在MAC帧的首部

##### 地址解析协议ARP和逆地址解析协议RARP

![ARP和RARP的作用](/img/computer-network/ARP-and-RARP.png)

每一个主机都设有一个**ARP高速缓存**，里面有**本局域网上**的各主机和路由器的IP地址到硬件地址的映射表

ARP工作过程：

* **如果ARP高速缓存中有目标主机的IP地址**，就在ARP高速缓存中查出其对应的硬件地址，再把这个硬件地址写入MAC帧，然后通过局域网把该MAC帧发往此硬件地址

* **如果ARP高速缓存中没有目标主机的IP地址**
  
  1. ARP进程在本局域网上广播一个ARP请求分组

  2. 本局域网上的所有主机上运行的ARP进程收到此ARP请求分组

  3. 目标主机在ARP请求分组中见到自己的IP地址，就向主机A发送ARP响应分组（ARP请求分组是广播发送的，ARP响应分组则是单播）

  4. 源主机收到目标主机的ARP响应分组，在其ARP高速缓存中写入目标主机的IP地址到硬件地址的映射

ARP的四种工作情况：

1. **主机 -> 本网络主机：**ARP找到目的主机的硬件地址

2. **主机 -> 另一个网络主机：**ARP找到本网络的一个路由器的硬件地址。剩下的工作由这个路由器完成

3. **路由器 -> 本网络主机：**ARP找到目的主机的硬件地址

4. **路由器 -> 另一个网络主机：**ARP找到本网络的一个路由器的硬件地址。剩下的工作由这个路由器完成

##### IP数据报格式

![IP数据报格式](/img/computer-network/IP-datagram-format.png)

IP数据报=首部（20字节固定长度部分+可变长度）+数据

路由分组转发算法：

1. 从数据报的首部提取目的主机的IP地址D，得出目的网络地址N

2. 若N是此路由器直接相连的某个网络地址，则进行直接交付；否则就是间接交付，执行下一步

3. 若路由表中有目的地址为D的特定主机路由，则把数据报传送给路由器中所指明的下一跳路由器；否则执行下一步

4. 若路由表中有到达网络N的路由，则把数据报传送给路由表中所指明的下一跳路由器；否则执行下一步

5. 若路由表中有一个默认路由，则把数据报传送给路由表中所指明的默认路由器；否则报告转发分组出错

### 划分子网和构造超网

##### 划分子网

1. **从两级IP地址到三级IP地址**

   划分子网的基本思路：

   * 本单位以外的网络看不见这个网络是由多少个子网组成，这个单位对外仍然表现为一个网络

   * 从网络的主机号借用若干位作为子网号subnet-id，两级IP地址在本单位内部变为三级IP地址：网络号、子网号和主机号

   * 路由器收到IP数据报后，再根据目的网络号和子网号找到目的子网，把IP数据报交付给目的主机

2. **子网掩码**

   子网掩码中的1对应于IP地址中原来的net-id和subnet-id，而子网掩码中的0对应于现在的host-id

   ![子网掩码](/img/computer-network/subnet-mask.png)、

   默认子网掩码中1的位置和IP地址中的网络号字段net-id正好相对应

   划分子网的路由器分组转发算法：

   1. 从收到的数据报的首部提取目的IP地址D

   2. 对路由器直接相连的网络逐个进行检查：用各网络的子网掩码和D逐位与操作，如果和相应的网络地址匹配，则直接交付；否则，执行下一步

   3. 若路由表中有目的地址为D的特定主机路由，则把数据报传送给路由表中所指明的下一跳路由器；否则，执行下一步

   4. 对路由表中的每一行，用其中的子网掩码和D逐位与操作得到N，若N与该行的目的网络地址匹配，则把数据报传送给该行指明的下一跳路由器；否则，执行下一步

   5. 若路由表中有一个默认路由，则把数据报传送给路由表中所指明的默认路由器；否则报告转发分组出错