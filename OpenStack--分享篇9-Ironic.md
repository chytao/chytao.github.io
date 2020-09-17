---
title: "OpenStack  分享篇9 Ironic"
date: 2020-05-13T17:57:48+08:00
draft: true
---

https://docs.openstack.org/ironic/latest/user/index.html

云计算技术的快速发展，同样带动了虚拟化技术的广泛应用，云计算和虚拟化的发展是一个相互促进的过程。虚拟机的特性很好的切合了云计算的需求，在裸机上部署和运行工作负载的方式似乎不再是主流了，但是在将工作负载往云端进行迁移的时候，就会发现在裸机上运行负载的方式同样是必不可少的。

​	高性能计算：采用物理机直接运行负载可以减少由虚拟化层带来的开销，提高IO的负载能力。

​	需要用到物理设备资源的计算任务，这些物理资源不能被虚拟化。

​	数据库应用：数据库服务对系统的IO要求较高，在虚拟化环境上运行会对性能带来一定的影响

​	裸机托管：客户把自己的物理机放到云服务商的云环境中，需要云服务提供商能够提供托管裸机

​	提供快速部署云基础架构的能力。

​	混合云的快速发展使得云环境中对虚拟机和物理机的部署和管理需要统一的接口，SoftLayer，Oracle等云环境已经实现了对裸机的云管理。

具有讽刺意味的是，Ironic作为一个OpenStack项目，他提供裸机（而不是虚拟机）他可以集成到OpenSatck中作为OpenStack云中的一部分使用，并与OpenStack中的各个项目keyStone，Nova，Neutron，Swift等协同工作。

## Ironic System Architectural



![88900096193900620](https://gitee.com/hanstack/hanstack_image/raw/master/image/88900096193900620.jpg)

Ironic每个交互的类型都是实际硬件的操作，比如电源，启动，部署等。根据物理硬件的不同，Ironic提供了一个驱动的框架来进行支持。目前已经实现的驱动有PXE-IPMITool，PXE-IPMINative，PXE-SSH等

Ironic-API通过RPC与Ironic-Conductor之间进行通信。Ironic-API的执行过程与使用方式与之前的OpenStack中的其他组件十分类似。Conductor的使用理念几乎脱胎于Nova-Conductor服务，用户提高数据库的并发性能和完成复杂流程的控制。

Ironic接收到用户的RESTful请求后通过RPC与Ironic-Conductor只见那进行通信，由Conductor完成复杂流程的控制。Conductor会根据配置需求调用不同的驱动，比如使用PXE完成真正的部署。

Ironic服务有以下两种使用模式

​	和Nova服务一起，作为Nova的一个virtDriver来使用，Nova把裸机当成是一个虚拟机来使用。利用Nova服务中的多租户，调度，配额管理等功能，构成真正的逻辑云。

​	Standalong单独使用模式，没有多租户，没有调度，没有配额管理等功能，更多的是一种逻辑的云部署工具。

![907042556495208419](https://gitee.com/hanstack/hanstack_image/raw/master/image/907042556495208419.png)

Nova-API发出boot Instance的请求，Nova-Scheduler通过消息队列拿到这个请求

Nova-Scheduler筛选出合适的裸节点；Nova-Compute根据Nova-Scheduler筛选出来的裸节点，通过Nova-Compute调度Ironic-virtDriver启动一个任务，通过Ironic-API查找该节点的信息，预留出该节点

将请求的Image从Glance上下载下来保存早Ironic Conductor的本地磁盘

调用Neutron-API来做网卡绑定并设置Neutron中的DHCP端口来支持PEX/TFTP服务

Ironic virtDriver通过Ironic API发出部署请求给Ironic Conductor

Ironic Conductor调度相应的驱动来完成部署。



## Ironic Driver

![229049767863961913](https://gitee.com/hanstack/hanstack_image/raw/master/image/229049767863961913.jpg)

根据不同的硬件，目标机哼够使用不同的方式进行部署，Ironic提供了一个驱动框架来进行支持，每种驱动基本都要实现Deploy，Power，Console等几类核心功能。

### Ironic API

Ironic API是Ironic用来接收RESTful请求的组件，它包含几个核心模块。

​	Chassis：表示一个机架或者机柜，他是Node的集合

​	Driver：表示服务里面的各种驱动资源，包含了镜像安装，部署，电源控制等。

​	Node：表示注册在Ironic中的一个物理机

​	Port：表示Neutron中的网络端口，用来绑定物理机器上的网卡端口。

### Ironic Conductor

Ironic API通过RPC调用Ironic Conductor来完成实际的工作，Ironic会根据配置交给具体的驱动，来完成真正的部署。