---
title: "OpenStack  分享篇5 Cinder"
date: 2020-05-08T17:40:18+08:00
draft: true
---

OpenStack系统架构

https://docs.openstack.org/ceilometer/latest/contributor/architecture.html

https://docs.openstack.org/install-guide/overview.html#block-storage

### Cinder的简介

https://docs.openstack.org/cinder/latest/

Cinder前身是Nova中的Nova-Volume服务，在Folsom版本发布，从Nova中剥离成为了一个独立的OpenStack项目。

Cinder能够为Nova虚拟机，Ironic裸金属服务器，容器等提供持久化的快存储能力。

### Cinder是用来干嘛的

Cinder与Nova通过本地存储为虚拟机提供临时快存储不同，Cinder类似于Amzon的EBS（Elastic Block Storage），为虚拟机提供持久化的块存储服务。Cinder虚拟机存储卷（Vomule）的生命周期的管理，包括创建，删除，卸载，快照等。

![736473343809177460](https://gitee.com/hanstack/hanstack_image/raw/master/image/736473343809177460.png)

在上图中展示了Cinder组件中的几个重要组成部分，包括Cinder-API;Cinder-Volume;Cinder-Scheduler；Cinder-Backup。他们之间通过AMQP消息队列进行通信。

-Cinder-API是进入Cinder的HTTP接口，负责接收来自外部的RESTful 的请求。

-Cinder-volume运行在存储节点管理具体存储设备和存储设备的存储空间，每个存储节点上都会运行一个Cinde-volume服务，多个这样的节点一起构成了一个存储资源池。

-Cinder-Scheduler是根据既定的策略（如不同的调度算法）选择合适的Cinder-Volume节点来处理用户的请求。在用户没有指定具体的存储节点时Cinder-Schedule会选择一个合适存储节点，并由该节点上的Cinder-volume响应该请求。当用户指定了存储节点之后，直接由该节点上的Cinder-volume响应该请求，不经过Cinder-volume.

举个例子说明一下：假如你去足疗店做足疗，你跟前台说一下你想做的项目。此时你没有相熟的足疗技师，你就也不知道那个技师的手法比较好，此时足疗店的经理会帮助你选择一个合适的足疗技师给你做足疗。还有一种情况是你去做足疗店做足疗，你知道哪个技师的手法比较好，你还想找这个足疗技师为人民服务。此时你只需要向前台报一下技师的编号或者技师留给你的小卡片什么的就可以了，不需要让经理帮你选择。

-Cinder-Backup用于提供存储卷备份功能，支持块存储卷备份到OpenStack备份存储后端，如Swift，Ceph，NFS等。

### Cinder是什么

Cinder在虚拟机与具体设备之间引入了“逻辑存储卷”的抽象层，但是Cinder并非是一种存储技术，Cinder并没有实现对快设备的实际管理和服务。他只是提供一个中间抽象层，作为一个中间件，为后端的不同存储技术，比如DAS，NAS，SAN（https://www.rfwireless-world.com/Terminology/difference-between-DAS-NAS-and-SAN.html），对象存储以及分布式文件系统等，提供了统一的接口，不同的块设备厂商在Cinder中通过驱动的形式实现这些接口来与OpenStack进行整合。

--https://wiki.openstack.org/wiki/CinderSupportMatrix

Cinder默认使用LVM（Logic Volume Mananger）作为后端存储（Backend Storage）。

​	

### 什么是LVM

​	在Linux系统中使用fdisk工具进行分割并管理磁盘的分区，比如讲磁盘的/dev/sda分割为/dev/sda1与/dev/sda2两个分区分别满足不同的需求，需要重新引到分区才能生效。

![696309627435642331](https://gitee.com/hanstack/hanstack_image/raw/master/image/696309627435642331.png)

LVM（Logic Volume Manager）本质上是一个虚拟设备的驱动，是在内核中设备和物理模块之间添加的一个新的抽象层。他可以将几块次哦按（物理卷，PhysicalVolume）组合形成一个存储池或者卷组（VolumeGroup）。LVM能够从存储池或者卷则中（VolumeGroup）中划分出不用大小的逻辑卷（Logic Volume）创建新的逻辑设备。底层的原始磁盘不在由内核直接控制，而由LVM层来控制。对于上层应用来说卷组替代了磁盘块成为数据存储的基本单元。LVM管理着所有物理卷的物理盘区，维持着逻辑盘区和物理盘区之间的映射。LVM逻辑设备向上层应用提供了和物理磁盘相同的功能，如文件系统的创建和数据的访问等。但LVM逻辑设备不受物理约束的限制，逻辑卷不必是连续的空间，它可以跨越许多物理卷，并且可以在任何时候任意的调整大小。相比物理磁盘来说，更易于磁盘空间的管理。

![385680443593809189](https://gitee.com/hanstack/hanstack_image/raw/master/image/385680443593809189.png)

从用户态应用来看，LVM逻辑卷相当于一个普通的块设备，对其的读写操作和普通的块设备完全相同。而从物理设备层来看，LVM相对独立于底层的物理设备，并且屏蔽了不同物理设备之间的差异。因而在LVM层上实现数据的连续保护问题，可以不需要单独考虑每一种具体的物理设备，避免了在数据复制过程中因物理设备之间的差异而产生的问题。从LVM的内核实现原理上看，LVM是在内核通用块设备层到磁盘设备驱动层的请求提交流之间开辟的另外一条路径，即在通用块设备层到磁盘设备驱动层之间插入了LVM管理映射层用于截获一定的请求进行处理。

用户通过lvm提供接口，依靠内核创建一系列LVM逻辑卷，所有对lvm逻辑卷的读写操作最终都会由LVM在通用块设备层下方截获下来，进行更进一步的处理。这里的进一步处理主要指的是完成写请求的映射，是将请求的数据根据实际情况进行一些拆分和重定位操作，从而可以将请求和数据分发到实际的物理设备中去。



### Cinder-API

Cinder-API 实现了对Volume，Volume类型（Volume Type）以及Snapshot的管理操作。

Volume类型是用户自定义的卷的一种标识。Cinder提供了相关的API可以自由的创建删除volume类型。

snapshot是一个Volume在某一个特定的时间点的一个快照，因此，Snapshot是只读的，不可以被改变的，Snapshot可以被用来创建一个新的Volume。

### Cinder-Scheduler

与Nova-Scheduler类似，Cinder的调度服务Cinder-Scheduler也用于选择一个合适的节点，不过不同的是，Nova-Scheduler选择的是计算节点来相应用户有关虚拟机的生命周期相关的请求，而Cinder-Sceduler选择的是Cinder-Volume节点来处理用户有关的生命周期的处理。

目前Cinder中实现了一个调度器FilterScheduler，但是在历史上曾经存在SimpleScheduler（选择剩余存储空间最多的Host）和ChanceFilter(随机挑选满足条件的Host)两个调度器，但是他们现在已经被利用FilterScheduler的框架重新实现。

FilterScheduler的工作流程和Nova的FilterScheduler调度器相同。

![896865148032422293](https://gitee.com/hanstack/hanstack_image/raw/master/image/896865148032422293.png)

#### Filtering（过滤）与weighting(权重计算与排序)

Filtering就是使用配置文件指定的各种Filters过滤掉不符合条件的主机，Weighting则是指对所有符合条件的主机计算权重（weight）并排序，从而得出最佳的一个。Cinder中已经实现了几种不同的Filter和weigher。

| 过滤器                 | 功能                                                         |
| ---------------------- | ------------------------------------------------------------ |
| AvailabilityZoneFilter | 按可用性区域过滤后端。                                       |
| CapabilitiesFilter     | BackendFilter与资源（实例和卷）类型记录一起使用              |
| CapacityFilter         | 基于卷后端的容量利用率的容量过滤器                           |
| DifferentBackendFilter | 将卷安排在与一组卷不同的后端上。                             |
| DriverFilter           | DriverFilter根据“过滤器功能”和指标过滤后端。                 |
| InstanceLocalityFilter | 通过此筛选器，可以选择运行实例的虚拟机管理程序的主机上的存储后端。这提供了数据局部性：实例和卷位于同一台物理计算机上。 |
| JsonFilter             | 后端过滤器，用于选择后端的基于JSON的简单语法                 |
| RetryFilter            | 筛选出以前尝试过的主机                                       |
| SameBackendFilter      | 将卷安排在与另一个卷相同的后端上                             |

### Cinder-Volume

类似于Nova中虚拟机的生命周期由nova-compute服务来管理，Cinder中Volume的生命周期由Cinder-Volume服务来管理。

#### ISCSI Target

​	基于ISCSI能够以较低的门槛实现SAN的应用。iSCSI属于传输层的协议，规定了ISCSI target和ISCSI Initiator之间的通信机制。ISCSI Initiator则是指能够基于ISCSI协议访问Target的客户端软件。

​	OpenStack中，Cinder创建Volume之后通常以ISCSI Target的方式提供给Nova。Nova通过ISCSI协议将该volum连接到计算节点上供虚拟机使用。

![](https://gitee.com/hanstack/hanstack_image/raw/master/image/127582079404373640.png)

#### 后端存储Dirver

为了支持不同的后端存储技术和设备，Cinder创建了一个Driver框架，在这个框架中包含了众多接口。可以通过指定使用哪种后端存储的Driver，且以哪种方式提供ISCSI Target。

### Cinder-backup

Cinder-backup用于将volume备份到其他存储系统上。目前支持的备份存储系统有Ceph，IBM Tivoli Storage Manger(TSM),GlusterFS等，默认为Swift。

不同的备份存储系统以Driver的形式得以支持.当前Cinder只支持一个备份存储后端。从Mitaka开始，Backup服务和volume服务解除紧耦合，不需要装在同一台主机上，Cinder-backup服务在接到请求后任意挑选一个Backup host来提供备份服务。

#### 	创建备份

​	Cinder-Backup通过RPC请求Cinder-Volume服务提供需要备份的卷。如果需要备份的卷处于available状态，直接把改卷返回给cinder-backup中。如果需要备份的卷正在被使用，则先根据该卷创建一份快照或者链接克隆卷。返回快照或者克隆卷给Cinder-backup.Cinder-Backup收到备份卷后，把备份卷挂载到本机，将数据备份到后端备份存储。

![869172021098627277](https://gitee.com/hanstack/hanstack_image/raw/master/image/869172021098627277.png)

#### 	恢复备份

​	cinder-backup将需要进行数据还原的卷挂载到本机，将数据从备份存储读出，恢复到卷上。

#### 	删除备份

​	cinder-backup直接调用Backup Driver中的接口进行删除。

### 创建卷过程

https://docs.openstack.org/cinder/latest/contributor/architecture.html

![architecture](https://gitee.com/hanstack/hanstack_image/raw/master/image/architecture.png)

1.Cinder-API监听RESTful创建卷的请求，Cinder-API接收到请求后将创建卷的请求通过RPC发送给Cinder-Scheduler

2.Cinder-Scheduler接收到请求后经过过滤和权重选择出创建卷的节点

3.Cinder-Volume在节点上通过Driver的形式调用后端存储的资源完成卷的创建。

4.将创建好的卷通过ISCSI的方式提供给创建好的计算实例（instence）使用