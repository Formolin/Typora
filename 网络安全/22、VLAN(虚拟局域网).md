# VLAN(虚拟局域网)

## 一、概述

为实现交换机以太网的广播隔离，一种理想的解决方案就是采用虚拟局域网技术。这种对连接到第2层交换机端口的网络用户的逻辑分段技术实现非常灵活，它可以不受用户物理位置限制，根据用户需求进行VLAN划分；可在一个交换机上实现，也可跨交换机实现；可以根据网络用户的位置、作用、部门或根据使用的应用程序、上层协议或者以太网连接端口硬件地址来进行划分。

广播的危害：增加网络/终端负担，传播病毒，安全性

如何控制广播？
路由器隔离（物理隔离），成本高，不灵活

## 二、VLAN设置

采用新的技术VLAN来控制广播，VLAN 技术是在交换机上实现的是通过逻辑隔离划分的广播域。
VLAN是控制广播，逻辑隔离广播域。
一个VLAN = 一个广播域 = 一个网段
VLAN的类型：

1. 静态VLAN：手工配置，基于端口划分的VLAN。
2. 动态VLAN：手工配置，基于MAC地址划分的VLAN。

```bash
1）创建VLAN
conf t
vlan id,id 
name 名字

2）查看VLAN
show vlan brief  查看vlan的简要信息

3）将端口加入到VLAN
int f0/x
switchprot access vlan id
1234567891011
```

![在这里插入图片描述](22、VLAN(虚拟局域网)/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109174555131.png)
**vlan默认配置列表**

默认所有端口都在vlan1里面
![在这里插入图片描述](22、VLAN(虚拟局域网)/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109174555254.png)
在同一个交换机上两个端口在同一vlan下的pc可以通信
![在这里插入图片描述](22、VLAN(虚拟局域网)/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109174555155.png)
入口和出口不在同一vlan的两台PC无法通信
PC1与PC3无法通信
![在这里插入图片描述](22、VLAN(虚拟局域网)/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109174555302.png)

## 三、Trunk(中继端口)

### 1、trunk/中继链路/公共链路

作用：trunk端口不属于任何VLAN，允许任何vlan的数据通过。
方法：通过在数据帧上加标签，来区分不同vlan的数据。

### 2、trunk标签

1）ISL标签：cisco私有的，标签大小30字节 26+4。加在帧的两边，前面加26字节，后面加4字节。

2）802.1q：公有协议，所有厂家都支持，标签大小4字节。属于内部标签。将帧切开，标签加在帧的中间4个字节。

### 3、交换机端口链路类型

1）接入端口：也成为access端口，一般用于连接PC，只能属于某一个vlan，也只能传输一个vlan的数据。
2）中继端口：也成为trunk端口，一般用于连接其他交换机，属于公共端口，允许所有vlan的数据通过。

### 4、配置命令

```bash
int f0/x
switchprot trunk encapsulation dot1q/isl	可选的协议
switchprot mode trunk
exit
1234
```

一旦端口配置成trunk端口，那么该端口不会在vlan表里出现！！！

**配置trunk主要解决了跨交换机相同vlan的PC间的通信。**