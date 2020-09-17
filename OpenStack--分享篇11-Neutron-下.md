---
title: "OpenStack  分享篇11 Neutron 下"
date: 2020-08-06T15:23:22+08:00
draft: true
---

![image-20200806143240044](https://gitee.com/hanstack/hanstack_image/raw/master/image/neutron的三大模型.png)

计算节点的实现模型小结

![image-20200806140100679](https://gitee.com/hanstack/hanstack_image/raw/master/image/计算节点实现模型.png)

1.用户网络层：

用户网络层（user network），指的是OpenStack的用户创建的网络，也成为外部网络。外部网络是相对于Host内部网络而言的。用户网络层对用的Bridge是br-ethx或者是br-tun。br-ethx对应的是Flat，Vlab等非隧道型二层网络。br-tun队形的是VxLan，GRE等隧道型的二层网络。实现载体一般是OVS，用户网络层的功能是将用户网络（外部网络）和本地网络进行相互转换。如VID的转换，VXLAN的封装和解封装。

2.本地网络层

本地网络层指的是Host主机内部的本地网络。由于用户网络层（Local network）对这一层的屏蔽，本地网络层只需要感知一种技术：VLAN。本地网络层分为两层。qbr的实现载体是Linux-Bridge，它仅仅负责安全，称之为安全层。br-int的实现载体一般是ovs，它负责内部交换，成为Bridge层。

​	bridge层是对VM层的一个屏蔽，从VM发出的Untag报文，被Bridge层转换为Tag报文转发到br-ethx/br-tun；从br-ethx/br-tun转发到br-int的Tag报文被br-int剥去tag，编程un-tag报文再转发给VM。



在同一个host的本地网络中的不同VM进行通信，他们经过本地网络层（br-int）即可完成。不需要再经过用户网络层。

![image-20200806142208478](https://gitee.com/hanstack/hanstack_image/raw/master/image/VM之间的通信.png)

Neutron-网络节点实现模型

![image-20200806151424790](https://gitee.com/hanstack/hanstack_image/raw/master/image/VM访问Internet.png)

一个VM想要访问外部网络，必须要到达数据中心的网关（GW）。接下来我们讨论的问题实际上是VM如何到达数据中心的网关。

在Neutron中的一个假设模型：

1.所有计算节点中的VM，要访问Internet，必须经过网络节点，网络节点作为第一层网关。

2.网络节点会连接到DC物理网络中的一个设备（或者是交换机或者是路由器）通过这个设备上联到DC的GW。此设备我们成为第二层网关。

3.DC网关再连接到Internet上。

Neutron并不关心上述的2,3点。对于Neutron来说第二层网关，DC External network和第三层网关（DW3）都不属于neutron管理范围，是DC运营商提前规划好的网络。这些网络对于Neutron统称为External Network或者Public Network。

 	Neutron在网络节点中部署了路由器。这个路由器也是是虚拟路由器，利用的是Linux内核功能。

![image-20200806150817611](https://gitee.com/hanstack/hanstack_image/raw/master/image/网络节点.png)

从OpenStack的J版本开始引入了DVR特性，DVR部署在计算节点上。计算节点访问internet,不必经过网络节点，直接从计算节点的DVR就可以访问。

![image-20200806143240044](https://gitee.com/hanstack/hanstack_image/raw/master/image/neutron的三大模型.png)

控制节点并没有具体的网络功能，它只是对各种虚拟网元做管理配合工作。控制节点部署OpenSatck的各种进程，对于Neutron来说，他的进程名是neutron-server。

控制节点的Neutron集成通过RESTful或者CLI接口接收外部请求，通过RPC与Agent进行交互。Neutron进程与各个Agent共同完成控制任务。



