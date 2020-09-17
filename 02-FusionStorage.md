# 02-FusionStorage Block

## 01-FusionStorage简介

FusionStorage块存储是一款可大规模横向扩展的存储产品，通过存储系统软件将服务器的本地存储资源组织起来，构建全分布式存储池，通过SCSI和iSCSI接口向上层应用提供块存储服务，满足云资源池及数据库等场景的存储需求。

FusionStorage块存储可提供HyperSnap（快照）、HyperReplication（远程复制）和HyperMetro（双活）、SmartDedupe（重复数据删除）和SmartCompression（数据压缩）等丰富的企业级数据服务特性，帮助企业轻松应对业务快速变化时的数据灵活、高效存取需求。

- 资源池

  将通用存储服务器池化，建立大规模块存储资源池。可以和各种虚拟化平台集成，如华为FusionSphere、VMware vSphere、开源OpenStack等，按需分配存储资源，支持各种业务应用(如SQL、Web、行业应用等等)。

  ![image-20200908220458110](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908220458110.png)

- 数据库

  以SSD做Cache或主存，极大的提升了存储系统的性能，又保留了分布式存储的高扩展性基因，从而解决了企业关键数据库、关键ERP（Enterprise Resource Planning）/CRM（Customer Relationship Management）等应用的高性能需求。
  
  ![image-20200908220428085](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908220428085.png)

FusionStorage存储架构图：

![image-20200908201311226](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908201311226.png)

## 02- FusionStorage 块存储特性

- 精简配置：FusionStorage 块存储提供了精简配置功能，为应用提供比实际物理存储更多的
  虚拟存储资源。相比直接分配物理存储资源，可以显著提高存储空间利用率。
  采用DHT 路由技术，系统无需使用专门的集中元数据来记录卷的精简分配情
  况，和传统SAN 相比，不会带来性能下降。

  ![image-20200908202929326](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908202929326.png)

- 快照：FusionStorage提供了快照机制，将用户的卷数据在某个时间点的状态保存下来，后续可以作为导出数据，恢复数据之用。

  FusionStorage快照在存储时采用ROW（Redirct-On-Wirte）机制，快照不会引起源卷性能下降。

  ![image-20200908212855812](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908212855812.png)
  
  

​            

- ​	共享卷快照：为了支持共享卷备份能力，FusionStorage也支持共享卷的快照。

  - 存在多个挂载点的SCSI卷被称为共享卷，对于ISCSI卷而言，所有的ISCSI卷都是共享卷。和普通卷快照流程不同，由于共享卷存在多个挂载点，多个挂载点的VBS都可能下发IO，在对共享卷打快照时需要多个节点协同配合来完成悬挂IO的操作，FusionStorage采用两阶段来实现该功能。

  - 第一阶段：由主VBS向所有的挂载点VBS发出prepare消息，挂载点VBS在收到prepare消息后进行IO的悬挂操作，并回复OK给主VBS。

  - 第二阶段，主VBS向所有的挂载点VBS发送commit消息，并带上需要更新的卷元数据信息，参与者在接收到消息后跟新本地元数据信息并解开IO悬挂，然后回复OK给主VBS后，完成本次事务。

    ![image-20200908213646917](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908213646917.png)

- 一致性快照：一致性快照用于整机备份，一个虚拟机通常挂载了多个卷，对虚拟机做整机备份时，所有的卷快照应当处于同一时间点上，才能保证数据恢复的可靠性。

  - FusionStorage具有一致性快照的能力，对于上一层发起的一致性快照请求会尽量保证多个卷的快照属于同一个时间点。

  - FusionStorage对多个卷执行悬挂IO后，再更新快照信息操作，尽量保证了多个卷快照时间点的一致性。

    ![image-20200908214311122](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908214311122.png)

- QoS：FusionStorage块存储提供了对卷的IOPS，带宽进行精细化控制，并提供burst功能，当卷的需求超出了基准IOPS或带宽时，允许在一定时间内使用超出基准性能的配额。

- 双活（Active-Active）：基于AB两个数据中心的两套FusionStorage块存储集群构建双活容灾关系，基于两套FusionStorage的卷虚拟出一个双活卷，两数据中心业务的主机能同时对卷进行读写。任意数据中心故障，数据零丢失，业务能迅速切换到另一个站点进行运行，保证业务的连续性。

  FusionStorage引入复制集群，按服务化的架构提供双活业务。支持物理部署和虚拟机部署们可以做到独立安装和升级，独立扩展，按卷力度提供双活服务。

  FusionStorage块存储双活支持优先站点仲裁和第三方仲裁的双仲裁模式，故障自动倒换，无需人工接入。

  FusionStorage块存储双活能跟上层Oracle RAC，VMware等应用形成端到端的双活容灾解决方案。

- 链接克隆：

  FusionStorage块存储是提供链接克隆机制，支持基于一个卷快照创建出多个克隆卷，各个克隆卷刚创建出来时的数据内容与卷快照中的数据内容一致，后续对于克隆卷的修改不会影响到原始的快照和其他克隆卷。

  支持1:2048的连接克隆比，提升存储空间利用率

  克隆卷继承普通卷所有功能；克隆卷可支持创建快照，从快照恢复以及再次作为母卷进行克隆操作。

  ![image-20200908221641838](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908221641838.png)



## 03 FusionStorage产品特点

- 高弹性和扩展性

  FusionStorage 块存储采用 DHT（Distributed Hash Table，分布式哈希表）架构，将**所有元数据按规则分布在各存储节点**，不存在跨节点的元数据访问，彻底避免了元数据瓶 颈。该核心架构设计保证了FusionStorage  块存储具备大规模的线性扩展能力。

  FusionStorage 块存储采用了独特的数据分片技术;以及基于 DHT 的数据路由算法，可以将卷的数据均匀的分散到较大的资源池故障域范围内，使得每个卷可以获得更大的IOPS 和 MBPS 性能，也使得每个硬件资源的负载相对均衡。另外多个卷共享资源池范围内的所有硬件设施，在应用负载多变的情况下，资源可以很好的在各个应用之间弹 性，不会出现传统的按盘做 RAID 导致的负载不均衡。

- 高性能

  FusionStorage 块存储通过动态智能分区、静态选盘算法以及自研 EC 算法，采用小IO 聚合、大 IO 直通等技术，在实现了业务均衡分布和保证了存储高可靠的同时，也保证了存储的高性能。在使用 HDD 作为主存时，采用分布式 SSD Cache 加速方案，将各个存储节点上的 SSD 组建成为一个共享的分布式 Cache 资源池，供所有的业务共同所用，实现 HDD 主存的性能加速。

- 高可靠性

  FusionStorage  块存储采用分布式集群管理方式，系统全冗余部署，无单点故障。支持多副本和EC（Erasure Coding）两种数据冗余保护机制，支持设置灵活的数据可靠性策略，支持 DIF(Data Integrity Field)、后台数据一致性校验等数据完整性校验功能。

  - 集群管理

    采用集群管理方式，从架构上保证了系统不会出现单点故障，一个节点或者一块 硬盘故障自动从集群内隔离出来，不影响整个系统业务的使用。

  - 多副本

    

     

    应用多副本机制可以将同一份数据复制 2~3 个副本，将不同的副本放在不同的服务器上，机柜上，甚至不同的机房，来保证在服务器故障、机柜故障、甚至机房 故障的情况下，数据仍然不丢失、仍然可访问。

    - EC（Erasure Coding）

    应用 Erasure Coding 技术实现节点间 RAID 功能，支持配置保护级别内的任意节点或任意磁盘故障而不影响数据完整性。相比于副本保护机制而言，EC 可靠性不降低、甚至高于副本机制，而空间利用率大幅提升。

    - DIF（Data Integrity Field）

    在存储系统接收到用户数据时，根据数据内容计算出摘要信息，插入 DIF 字段， 在后续数据流过的关键通道上设置校验点，如果数据在传输过程中发生了错误， 新计算出的摘要信息就和初始插入的摘要信息不一致，从而知道数据发生了错误，尝试自动修复并上报告警，确保数据的一致性。

    - 后台数据一致性校验

    支持 BST 及对硬盘的后台扫描功能，在数据读写访问硬盘时或者后台定期扫描过程中如果发现硬盘出现坏道，系统会自动进行数据修复。

    - 系统亚健康检测与处理

    通过性能的量化指标(例如时延)检测资源服务的异常状态，超过预设条件判断是否 为亚健康资源，系统对亚健康资源进行隔离，并恢复系统服务。

- 高安全性

  FusionStorage 块存储提供的安全保护机制主要体现在网络安全、操作系统安全、数据安全和管理安全。

- 数据保护

  FusionStorage 块存储提供以下数据保护功能：

  - 快照

  将用户的卷数据在某个时间点的状态保存下来，后续可以作为导出数据、恢复数 据之用。更多关于快照特性的描述请参见对应版本的《FusionStorage   块存储

  HyperSnap 特性指南》。

  - 远程复制

  将生产中心的数据周期性同步到异地灾备中心，实现系统容灾。更多关于远程复 制特性的描述请参见对应版本的《FusionStorage 块存储 HyperReplication 特性指南》。

  - 双活

  基于两套 FusionStorage 块存储集群构建 Active-Active 读写访问能力，任意一个数据中心故障，系统自动切换到另外一个集群运行，保证上层业务连续性。

- 智能、高效、经济

  FusionStorage  块存储采用以下机制和功能，提供智能、高效、经济的存储系统。

  - 精简配置

    当用户对卷进行写操作时才分配实际物理空间，来为用户提供比物理存储资源更 多的虚拟存储资源。

  - 重复数据删除和数据压缩

    通过重复数据删除技术对硬盘中重复的数据进行检查和处理，通过数据压缩特性 大幅减少数据所占空间，有效提高硬盘利用率。更多关于重删和压缩特性的描述 请参见对应版本的《FusionStorage 块存储 SmartDedupe&SmartCompression 特性指南》。

  - QoS 功能

    允许用户对应用设置优先级性能目标，从而将合适的资源提供给合适的业务，达 到保障关键业务性能的目的。更多关于 SmartQoS  的描述请参见对应版本的

    《FusionStorage 块存储 SmartQoS 特性指南》。

  - 缓存

    将分布到各个存储节点上的 SSD 组建成为一个共享的分布式 Cache 资源池，供所有的业务共同所用，提高读写性能的同时充分利用所有 SSD 的资源。

- 高易用性

  FusionStorage  块存储支持网络拓扑统一管理，管理维护高效集中，简洁易用。

  - 管理员可以通过 DeviceManager 管理 IP 地址访问 DeviceManager，完成对集群系统的监控、配置、性能统计和告警浏览等操作。

  - 当存在多套 FusionStorage  块存储，比如双活、远程复制等应用场景时，可通过eSight 实现集中管理和维护。

  - 支持对接 eService 云智能管理系统，通过大数据分析和 AI 人工智能技术驱动 IT

    运维活动，提前识别故障，降低运维难度，全面提升运维效率。

- 开放兼容

  FusionStorage 块存储具备广泛的兼容性：

  - 支持各种存储介质

  如 SATA 盘、SAS 盘、SSD 卡&NVMeSSD、SSD 盘、SATA 加密盘、SAS 加密盘等，这些介质都可以作为FusionStorage 块存储的主存。

  - 支持各种 Cache 介质

  SSD 卡&NVMeSSD、SSD 盘等。SSD 卡&NVMeSSD、SSD 盘属于保电 Cache， 在系统掉电的时候数据仍然不丢失。

  - 支持各种网络介质类型

  允许采用如 IB、RoCE 以及 TCP 等将存储服务器互联。

  - 支持广泛的虚拟化平台及应用

    支持工业界标准的 SCSI 和 iSCSI 接口，可以与各种虚拟化平台集成，如 XEN、KVM、VMware，包括此基础上的各厂家增强的虚拟化平台，如华为FusionSphere。FusionStorage 块存储同时支持各种的应用，如数据库相关的 SQLServer、Oracle RAC、DB2、Sybase 等，以及各种企业 IT 应用、各种行业类应用、web 类应用等。FusionStorage 块存储兼容的服务器类型、操作系统类型及版本、虚拟化平台及版本等信息可以通过[存储兼容性查询工具](http://support-open.huawei.com/zh/pages/user/compatibility/support-matrix.jsf)进行查询，具体查询方法请参见 [B.1  ](#_bookmark179)[兼容性](#_bookmark179)。

  

  

  ### 04 FusionStorage软件组件架构

  #### VBS部署在存储节点

  ![image-20200908223731470](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908223731470.png)

  

  ### VBS部署在计算节点

  ![image-20200908223756625](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908223756625.png)

  

  ###  组件介绍

  | 组件/进程    | **说明**                                                     |
  | ------------------- | ------------------------------------------------------------ |
  | FSM | FusionStorage 块存储的 Manager，为管理进程，实现系统的资源监控、配置、管理、升级和扩容等功能，必须主备管理节点部署。  安装管理节点时启动  FSM 进程。 |
  | FSA            | FusionStorage  块存储的 Agent，为管理代理进程，部署在各存储节点和计算节点上，实现各节点与 FSM 通信。  安装存储节点和计算节点时启动 FSA 进程。 |
  | MDC            | Metadata  Controller，为元数据控制组件，实现对分布式集群的状态控制，以及控制数据分布规则、数据重建规则等。  选择 3 个、5 个、7 个或者 9 个节点创建控制集群时，对应的控制集群节点上会启动 MDC 进程，由ZK 集群在多个 MDC 中选举主MDC，主 MDC 对其它 MDC 进行监控，主 MDC 故障时产生新的主  MDC。每个资源池有一个归属 MDC，增加资源池也会自动启动  MDC，一个系统最多启动  96 个 MDC。 |
  | ZK             | ZooKeeper，分布式应用程序协调服务进程。ZK 部署在控制集群的每个节点上，形成 ZK 集群，为 MDC 集群提供选主仲裁，必须保证大于总数一半的 Zookeeper 处在活跃可访问状态。  创建控制集群时，对应的控制集群节点上会启动 ZK 进程。 |
  | VBS            | Virtual Block  Service，VBS 通过 SCSI 或 iSCSI 接口提供分布式存储接入点服务，使应用服务器能够通过 VBS 访问分布式存储资源。  选择服务器启用 VBS 后，该服务器上启动 VBS 进程，部署 VBS 进程的服务器形成 VBS 集群。 |
  | EDS            | Enterprise Data Service，提供快照、重删、复制等企业级特性的服务。  加入存储池的服务器上会启动 EDS 进程。 |
  | OSD            | Object Storage Device，其功能是处理 VBS 下发的IO 消息，进行数据冗余保护并持久化到存储介质中。  加入存储池的服务器上会启动  OSD 进程，一个服务器上启动一个  OSD 进程。 |
  | CM             | Cluster Manager，集群管理进程，用于管理控制集群信息。当创建控制集群时，启动 CM 进程。 |
  | CCDB           | Cluster Configuration Database，集群配置数据库，存储集群的配置信息（如双活 Pair、远程复制 Pair 和一致性组信息）。  当创建控制集群时，启动 CCDB 进程。 |
  
  ### 组件的交互流程
  
  ![image-20200908231848300](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908231848300.png)



系统启动时，MDC与ZK互动决定主MDC。主MDC与其它MDC相互监控心跳，主MDC决定某MDC故障后接替者。其它MDC发现主MDC故障又与ZK互动升任主MDC

OSD启动时向MDC查询归属MDC,向归属MDC报告状态，归属MDC把状态变化发送给VBS。当归属MDC故障，主MDC指定一个MDC接管，最多两个池归属同一个MDC

VBS启动时查询主MDC，向主MDC注册（主MDC维护了一个活动VBS的列表，主MDC同步VBS列表到其它MDC，以便MDC能将OSD的状态变化通知到VBS），向MDC确认自己是否为leader；VBS从主MDC获取IO View，主VBS向OSD获取元数据，其它VBS向主VBS获取元数据

## 05-DHT算法

### 硬盘的结构

![202058055](https://gitee.com/hanstack/hanstack_image/raw/master/image/202058055.jpg)

### 硬盘的机械结构

![20200508057](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508057.jpg)







机械硬盘主要由磁盘盘片、磁头、主轴与传动轴等组成，数据就存放在磁盘盘片中。大家见过老式的留声机吗？留声机上使用的唱片和我们的磁盘盘片非常相似，只不过留声机只有一个磁头，而硬盘是上下双磁头，盘片在两个磁头中间高速旋转。





![20200507058](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200507058.jpg)





### 机械硬盘的逻辑结构

机械硬盘的逻辑结构分为磁道，扇区，柱面下图为磁道和扇区的逻辑图。



![20200508059](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508059.jpg)



什么是磁道呢？每个盘片都在逻辑上有很多的同心圆，最外面的同心圆就是 0 磁道。我们将每个同心圆称作磁道（注意，磁道只是逻辑结构，在盘面上并没有真正的同心圆）。硬盘的磁道密度非常高，通常一面上就有上千个磁道。但是相邻的磁道之间并不是紧挨着的，这是因为磁化单元相隔太近会相互产生影响。

那扇区又是十么呢？扇区其实是很形象的，大家都见过折叠的纸扇吧，纸扇打开后是半圆形或扇形的，不过这个扇形是由每个扇骨组合形成的。在磁盘上每个同心圆是磁道，从圆心向外呈放射状地产生分割线（扇骨），将每个磁道等分为若干弧段，每个弧段就是一个扇区。每个扇区的大小是固定的，为 512Byte。扇区也是磁盘的最小存储单位。

柱面又是什么呢？如果硬盘是由多个盘片组成的，每个盘面都被划分为数目相等的磁道，那么所有盘片都会从外向内进行磁道编号，最外侧的就是 0 磁道。具有相同编号的磁道会形成一个圆柱，这个圆柱就被称作磁盘的柱面



![20200508060](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508060.jpg)

硬盘的大小是使用"磁头数 x 柱面数 x 扇区数 x 每个扇区的大小"这样的公式来计算的。其中，磁头数（Heads）表示硬盘共有几个磁头，也可以理解为硬盘有几个盘面，然后乘以 2；柱面数（Cylinders）表示硬盘每面盘片有几条磁道；扇区数（Sectors）表示每条磁道上有几个扇区；每个扇区的大小一般是 512Byte。

于是在我们使用磁盘进行数据的存放时我们就可以根据磁盘的逻辑结构也就是CHS寻址方式进行数据的读写操作。也就是只要确定盘面号，柱面号，扇区号，就能往扇区中进行读写数据了。

LBA（Logic block address）：逻辑块寻址 （LBA） 是一种常见方案，用于指定存储在计算机存储设备上的数据块的位置，通常为辅助存储系统（如硬盘驱动器）。LBA是一种特别简单的线性寻址方案;块由整数索引定位，第一个块为 LBA 0，第二个 LBA 1，等等BA可以意指某个数据区块的地址或是某个地址所指向的数据区块。现今计算机上所谓一个逻辑区块通常是512或1024位组。ISO-9660格式的标准CD则以2048位组为一个逻辑区块大小。

LUN（logice unit number）：逻辑单元号。scsi总线上可挂接的设备数量是有限的，一般为6个或者15个，我们可以用target ID(也有称为scsi id的)来描述这些设备，设备只要一加入系统，就有一个代号，我们在区别设备的时候,只要说几号几号就ok了。 
  而实际上我们需要用来描述的对象，是远远超过该数字的，于是我们引进了lun的概念，也就是说lun id的作用就是扩充了target id。每个target下都可以有多个lun device，我们通常简称lun device为lun，这样就可以说每个设备的描述就有原来的target x变成target x lun y了,那么显而易见的,我们描述设备的能力增强了。

DHT(distributed hash table):FusionStorage中指的是数据路由算法，简单来说就是实现数据分布式存放的算法。

parttion：代表了一块数据分区，DHT环上的固定段代表的数据区。

key-value：底层磁盘上的数据组织成key-value的形式，每个value代表一个块存储空间。





### hash：

Hash，一般翻译做散列、杂凑，或音译为哈希，是把任意长度的输入（又叫做预映射pre-image）通过散列算法变换成固定长度的输出，该输出就是散列值。这种转换是一种压缩映射，也就是，散列值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出，所以不可能从散列值来确定唯一的输入值。简单的说就是一种将任意长度的消息压缩到某一固定长度的消息摘要的函数。

![20200508062](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508062.png)

在上图中我将一段话进行了hash，在加密结果这个文本框中得到了一串用16进制表示的数值。这个数值的范围是0到2^32-1.无论我们对任何长度的文本去执行Hash，得到的都是这个范围内的一串数值。

Hash算法还有一个特性就是即使是差别特别小的两个输入，得到的输出结果差别也是很大的，我们就是利用hash的这个特性来实现分布式的。下图中我在执行Hash的文本的末尾添加了一个逗号。再次执行hash然后和之前的hash结果进行对比。

![20200508063](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508063.png)

| 方式   | hash结果                         |
| ------ | -------------------------------- |
| 添加， | d493c72ce5453459b4b62937a08550f7 |
| 未加， | 55e191afee4e1b9b90d3676d669d5bda |

### DHT：

在上面我们已经知道了Hash的范围是0到2^32-1.我们将这个数值范围抽象成一个收尾相接的环变得到了hash环。也就是说hash环代表的是整个hash的取值范围。

![20200508070](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508070.png)

我们假设上图代表的是Hash环，那么显然此环的取值范围为0-2^32-1.但是在上图中为将这个Hash环平均分成了8部分，也就是说每部分都占整个Hash取值范围的八分之一。我们把每部分称之为一个partion，即一个分区。当然我在这幅图中只划分了8个部分，实际上在FusionStorag中他是将这个Hash环划分成了3600份。

### 磁盘和DHT

接下来我们说一下的磁盘和Hash环之间的关系。假设目前的磁盘数为36块。数据需要通过Hash环均匀的存放到这36块磁盘上。应该怎么做呢？

首先需要建立Hash环和磁盘之间的映射。这是什么意思呢？在这里我们用一个例子来说明一下Hash环和磁盘之间的映射关系。

假设有一个村子只能通过一个通过下雨来获得水源。然而每天下雨的地方有且只有一块空地。村子里有10口人，每个人都需要每天使用完全相同的小桶接收雨水维持日常生活。我们假设这个空地能容纳的小桶的个数为100个。为了让每个人尽量平均的获取每天所需的雨水所以，所以村子里每个人都分得10个小桶去空地接收雨水。10个村民得到的雨水基本上一致的。

![20200508065](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200508065.png)



回到磁盘和DHT，我们只要使得每个磁盘分配的分区数目相同就能够完成数据在磁盘上的均匀存放。也就是说每个磁盘分得了100个hash环上的partion分区。

比如disk1分得的hash环上的partion分区为0到99。

### 完整的DHT过程：

![2020050869](https://gitee.com/hanstack/hanstack_image/raw/master/image/2020050869.png)

os通过scsi或者iscsi指令发送key-value（LUN ID,LBA ,Data）给到VBS，VBS执行对key-value中的LUN ID和LBA复合起来的值执行hash计算。

计算来的Hash值一定在0-2^32-1这个范围区间内，我们已经将这个Hash区间进行抽象成Hash环，也就是说VBS计算出来的Hash值一定落在Hash环的某一个点上。

我们通过取模的方式将Hash环的范围区间分成了3600个Partion分区。那么我们查看一下VBS计算出来的Hash值落在了哪一个partion区间内。

Hash环上的partion区间和磁盘做了映射，查看一下这个partion分区属于哪一个Disk。我们就将key-value中的value代表的data存放到该硬盘上。

### Cache机制

#### write Cache机制：

OSD 在收到VBS 发送的写IO 操作时，会将写IO 缓存在SSD Cache 后完成本节点写操作。副本场景，SSD Cache 中的内容为IO 的完整数据；EC 场景，SSDcache 中的内容为数据或者冗余条带。同时，OSD 会周期将缓存在SSD Cache中的写IO 数据批量写入到硬盘，写Cache 有一个水位值，未到刷盘周期超过设定水位值也会将Cache 中数据写入到硬盘中。注：FusionStorage 支持大块直通，按缺省配置大于256KB 的块直接落盘不写Cache，这个配置可以修改。

![image-20200908225623703](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908225623703.png)

#### Read Cache机制：

FusionStorage 使用SSD 作为读Cache 介质以加速存储访问。FusionStorage 的读缓存采用分层机制，第一层为内存Cache，内存Cache 采用LRU 机制缓存数据；第二层为SSD Cache，采用热点读机制，系统会统计每个读取的数据，并统计热点访问因子，当达到阈值时，系统会自动缓存数据到SSD 中，同时会将长时间未被访问的数据出SSD。同时FusionStorage 支持预读机制，统计读数据的相关性，读取某块数据时自动将相关性高的块读出并缓存到SSD 中。OSD 在收到VBS 发送的读IO 操作时，会进行如下步骤处理：第1 步：从内存“读Cache”中查找是否存在所需IO 数据，如果存在，则直接返回，同时调整该IO 数据到“读Cache”LRU 队首，否则执行第2 步。第2 步SSD 的“读Cache”中查找是否存在所需IO 数据，如果存在，副本场景直接返回，EC 场景则获取其他节点上的数据块并通过EC 算法将数据合并后返回，同时增加该IO 数据的热点访问因子，否则执行第3 步。第3 步：从SSD 的“写cache”中查找是否存在所需IO 数据，如果存在，副本场景直接返回，EC 场景则获取其他节点上的数据块并通过EC 算法将数据合并后返回，同时增加该IO 数据的热点访问因子；如果热点访问因子达到阈值，则会被缓存在SSD 的“读cache”中。如果不存在，执行第4 步。第4 步：从硬盘中查找到所需IO 数据，副本场景直接返回，EC 场景则获取其他节点上的数据块并通过EC 算法将数据合并后返回，同时增加该IO 数据的热点访问因子，如果热点访问因子达到阈值，则会被缓存在SSD 的“读cache”中。

![image-20200908225933865](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908225933865.png)

#### 读IO流程

![image-20200908230111700](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908230111700.png)

APP 下发读IO 请求到OS

OS 转发该IO 请求到本服务器的VBS 模块

VBS根据读IO 信息中的LUN 和LBA 信息，通过数据路由机制确定数据所在的Primary OSD；

如果此时Primary OSD 故障，VBS 会选择secondary OSD 读取所需数据。

Primary OSD 接收到读IO 请求后，按照Cache 机制中的**“Read cache 机制”**获取到读IO 所需数据，并返回读IO 成功给VBS。

#### 写IO流程

![image-20200908230155202](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908230155202.png)

APP下发写IO请求到OS

OS转发该IO请求给到本服务器的VBS模块

VBS根据写IO信息中的LUN ID和LBA信息，通过数据路由机制确定数据所在的Primary OSD

Primary OSD接收到写IO的请求后，同时以同步方式写入到本节点SSD Cache以及数据副本所在的其他节点的secondary OSD（三副本场景：primary OSD 同时会写入到third OSD）

Secondary OSD也会同步写入本节点的SSD Cache。

Primary OSD接收到两个写成功后，返回写IO成功给VBS

SSD Cache中的数据会异步输入到硬盘。

## 06 FusionStorage块存储相对于传统存储的优势

- 更高的性能

  在和IPSAN对比测试中，在同样的测试条件下，FusionStorage相比IPSAN具有十倍以上的性能优势，随着支持的盘数越多，性能优势愈加显著。

  ![image-20200908231321360](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908231321360.png)

  

- 支持线性扩展Scale-up、Scale-out

  传统存储智能Scale-up，不支持Scale-out，即使在Scale-up的情况下，也很难保证性能的线性增长。受制于机头处理能力的影响，很难保证能够线性扩展。FusionStorage块存储随着盘数的增加，系统性能线性提升，

  ![image-20200908232221664](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908232221664.png)

  ![image-20200908232346212](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908232346212.png)

- 大池POOL，易于规划和管理

  ![image-20200908234118010](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908234118010.png)

  - 传统存储IPSAN对外提供业务前，需要组件不同的RAID组，然后再RAID上划分不同的LUN对外提供业务，由于可靠性的关系，一般RAID能够容纳的盘比较少，因此单个RAID组对外提供的业务性能有限。

    - 业务规划复杂，调整困难：当业务性能在原来 RAID 组上无法满足时，需要将该 LUN迁移到另外一个 RAID  组上。特别当出现系统总体性能还比较富裕，但是任何一个RAID 组都无法满足该 LUN 性能要求时，会需对多个 RAID 组，多个 LUN 进行调整。这种调整不仅对业务正常运行带来风险，而且很多情况下，即使大范围的整也无法满足性能要求，系统性能浪费严重，不同 RAID 组性能无法共享使用，如上图，有些RAID 组性能要求低，有些 RAID 组性能要求高，会造成系统整体系统资源虽然有很大冗余，但是业务无法使用的情况，导致浪费存在如下几种情况：

      - 不同业务性能要求不同：用户需要按照业务进行规划不同的 RAID 组，不同的 RAID组不能性能共享，导致性能浪费。

        同一类业务在不同时刻，对性能要求不同：系统需要按照该 RAID 组最大性能进行规划，而这个最大性能要求，可能只占一天/周/月很短的时间，但还是必须按照最大性能进行规划。大多数情况下闲置资源无法得到使用。

    - FusionStorage 块存储由于采用大资源池，所有的硬件资源都参与到任意一个务，用户只要保证系统整体性能和容量满足要求，就可以直接增加业务（新业务或原有业务提升性能）， 不需要额外进行性能规划和调整。面对 IPSAN 遇到的问题， 华为FusionStorage 能轻松应对，不仅使系统性能利用最大化，而且最大程度的降低用户维护投入，降低业务中断的风险。

      ![image-20200908234309937](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908234309937.png)

- SSD Cache 与 SSD Tier

  传统 IPSAN 存储使用机头内的内存作为存储 I/O 的 Cache，但是内存大小有限，一般配置 8G/16G/32G，这和所处理的业务需求相差非常大，特别是对于复杂混合多业务以及性能要求高的场景，机头内存 Cache 能够缓存的内容非常少，很难真正发挥明显的作用。因此传统存储厂商也相继启用 SSD 盘对存储业务进行加速，但是大多数存储厂商将它作为 SSD Tier 而不是 SSD Cache。SSD Tier 对于稳定单一，热点固定且热点持续时间长的业务比较有效，已经无法满足当前多业务，热点变化快的场景，特别是云业务场景。FusionStorage 块存储利用 SSD 作为 Cache 层，能及时感知业务热点变化，快速反应， 能持续保障高性能。

- 更加符合多业务混合应用场景

  随着 IT 技术的发展，虚拟化应用越来越多，系统对存储要求越来越高，原来单一的业务已经由多业务所代替。大量业务混合在一起，会对存储系统提出更高的要求：首先，多业务存储性能规划、实施和维护变得更复杂，更难以管理；其次，传统存储按RAID 组划分的方式，会进一步造成存储资源的浪费，无法得到高效的利用；最后因为多业务混合，系统业务热点将不再明显，热点数据变化非常频繁。SAN 这种基于单一业务设计的存储架构已经明显不能满足目前业务发展的新趋势。

  华为 FusionStorage 采用全局共享、自动均衡、无性能瓶颈等技术优势，不仅能保证系统资源得到最大程度的利用，而且还降低业务规划、实施和维护的复杂性，同时采用SSD Cache 实现快速响应业务变化，随时提供高性能存储性能，在多个市场获得好评。
## 07-块存储可靠性

- 集群的可靠性

  FusionStorage 块存储采用集群管理方式，从架构上保证了系统不会出现单点故障，一个节点或者一块硬盘故障自动从集群内隔离出来，不影响整个系统业务的使用。具体为：

  Zookeeper：Zookeeper 也称作 ZK，为 MDC 提供选主仲裁。ZK 还存储系统初始化时产生的元数据，包括“分区-硬盘”映射关系等数据路由信息。一个系统部署奇数个

  Zookeeper 组成集群，最少部署 3 个，必须保证大于总数一半的 Zookeeper 处于活跃可访问状态。一旦系统部署不能再扩容 ZK 数量。

  MDC：元数据控制软件，实现对分布式集群的状态控制。系统初始至少部署 3 个MDC 模块，增加资源池自动为该资源池启动或指定一个 MDC。多个 MDC 利用Zookeeper 选举一个主 MDC，主 MDC 监控其它 MDC，发现 MDC 故障则重启 MDC或为资源池指定托管 MDC。当主 MDC 故障时，通过选举产生新的主 MDC。

  OSD：对象存储设备服务，执行具体的 I/O 操作。采用主备模式，MDC 实时监控 OSD 的状态，当指定 Partition 所在的主 OSD 故障时，存储服务会实时自动切换到备 OSD， 保证了业务的连续性。

- 数据一致性

  数据一致性意味着当应用成功写入一份数据到存储系统时，存储系统的几个数据副本必然是一致的，当应用再次读时，无论在哪个副本上读取，都是之前写入的数据。

  FusionStorage 块存储采取多种方式来保证系统中数据的一致性：

  1．副本同步写

  当 VBS 模块将写操作发送给指定主 OSD 时，该 OSD 在写入本节点硬盘时，同时将写操作同步到备 OSD，同步过程中严格按照 IO 编号进行，保证了主 OSD 接收到的 IO 顺序和同步到备 OSD 的 IO 顺序一致。当主备 OSD 同时完成写操作时，才向应用返回操作成功。具体流程如下图所示：

  ![image-20200908235118607](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908235118607.png)

  2．读修复（Read Repair）

  Read Repair 机制是指在读数据失败时，系统会判断错误类型，如果是磁盘扇区读取错误，系统会自动从其他节点保存的副本读取数据，然后重新写入该副本数据到硬盘扇区错误的节点，从而保证数据副本总数不减少和副本间的数据一致性。

- 数据快速重建

  FusionStorage 每个硬盘都保存了多个数据块（Partition），这些数据块的副本按照策略分散在系统中的其他节点。当 FusionStorage 检测到硬盘或者节点硬件发生故障时，自动在后台启动数据修复。由于数据块的副本被分散到多个不同的存储节点上，数据修复时，将会在不同的节点上同时启动数据重建，每个节点上只需重建一小部分数据， 多个节点并行工作，有效避免单个节点重建大量数据所产生的性能瓶颈，对上层业务的影响做到最小化。数据故障自动重建流程如下图所示。

  ![image-20200908235310678](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200908235310678.png)

   FusionStorage 支持并行、快速故障处理和重建：

  - 数据块（Partition）及其副本分散在整个资源池内，硬盘故障后，可在资源池范围内自动并行重建。

  -  数据分布上支持跨节点，不会因某个节点故障导致的数据不可访问和不可重建。

  - 故障或者扩容时可以自动进行负载均衡，应用无需调整即可获得更大的容量和性能。

## 08-FusionStorage与虚拟化平台的兼容性

华为 FusionStorage 提供了分布式的块存储业务，分布式块存储支持与业界主流的虚拟化平台对接， 包括 VMware ESXi、以 Xen 和 KVM 为基础的增强虚拟化产品、Windows Hyper-V 虚化，以及华为的 FusionSphere 等。华为 FusionStorage 通过提供标准的 SCSI 和 iSCSI 向虚拟化平台提供块存储服务，其中 iSCSI 适用于 **VMware 和Hyper-V** 虚拟化场景。

#### FusionStorage的部署方式

- 融合部署

  - VBS和OSD部署在同一台服务器中（注：计算存储融合的部署模式是指服务器上既部署虚拟机，又部署存储软件）

  - 虚拟化应用推荐采用融合部署的方式

    ![image-20200909001706058](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909001706058.png)

- 分离部署

  - VBS和OSD分别部署在不同的服务器中（注：计算存储分离的部署模式是指服务器上仅部署虚拟机，存储软件采用单独的服务器部署）
  - 对性能要求较高的场景推荐采用分离部署方案

  ![image-20200909001721333](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909001721333.png)

#### VMware虚拟化场景：

- （计算存储）融合部署：

  ![image-20200909001911159](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909001911159.png)

- （计算存储）分离部署：

  ![image-20200909002001496](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909002001496.png)

- VMware场景下，VM使用FusionStorage存储空间的路径

  - VM通过虚拟IO总线发送SCSI流到ESXI
  - EXSI通过业务平面发送ISCSI流到CVM虚拟机上的VBS模块
  - VBS通过存储平面发送key-value到达OSD

#### Hyper-V场景

- （计算存储）融合部署：

  ![image-20200909002058697](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909002058697.png)

- （计算存储）分离部署：

  ![image-20200909002136423](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909002136423.png)

## 09-FusionStorage的网络平面及作用

- 管理平面：用于FSM和FSA之间的通信
- 存储平面：用于MDC、VBS、OSD之间的通信
- 业务平面：用于OSD与VBS之间的通信

![image-20200909002532310](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909002532310.png)

![image-20200909002639885](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909002639885.png)



## 10-FusionStorage整体的IO流程

![20200513021453](https://gitee.com/hanstack/hanstack_image/raw/master/image/20200513021453.jpg)

1.APP产生的写操作经由操作系统OS提供的API转发到OS。

2.OS通过将通过SCSI或者iSCSI协议将数据写操作传递给VBS。

3.VBS的SCSI Initator启动将数据转发到SCSI Target。

4.SCSI Target接收到数据后将SCSI消息发送到VBP模块，VBP模块将SCSI类型的数据转写为key-value的形式。

5.VBP将key-value转发转给client，client对key-value执行哈希计算。

6.VBP与VBM进行通信，VBM负责管理元数据，然后通过Agent发送给FSM；VBM也可以与VBS_CLi进行通信，开放修改VBS的命令行接口；VBM通过Heart Beat进行通信，通过心跳将元数据消息同步给MDC，MDC将消息交给ZK存到ZK盘或者ZK分区

7.经过Client计算的Hash值一定落在DHT所代表的Hash范围内，Hash的范围经过取模划分为3600个分区。分区和Disk在初始化时建立好了映射关系。这个partion分区和Disk之间的映射关系叫做IO View。

8.落在partion分区内的数据通过IO View找到数据要存放的具体磁盘。

9.一般一个磁盘由一个OSD进程进行管理，OSD将磁盘空间进行初始化，划分为单位大小为1M的空间。这个叫做OSD View。数据由OSD存放在磁盘的逻辑空间上。

10.FS通过副本机制来实现数据的可靠性，数据存放时分为主OSD和备OSD。partion分区和主备OSD之间的映射关系称为Partion View。

11.在OSD执行具体的数据落盘操作时，数据写通过主OSD的RSM一致性写模块，到达cache，然后到达写缓存，写缓存水位值满了之后数据到达AIO异步IO模块

12.AIO将数据SMIO落盘到Disk上。最后主备OSD进行数据的同步。





## 11-FUsionStorage强一致性的实现

数据一致性意味着当应用成功写入一份数据到存储系统时，存储系统的几个数据副本必然是一致的，当应用再次读时，无论在哪个副本上读取，都是之前写入的数据。

1．副本同步写

当 VBS 模块将写操作发送给指定主 OSD 时，该 OSD 在写入本节点硬盘时，同时将写操作同步到备 OSD，同步过程中严格按照 IO 编号进行，保证了主 OSD 接收到的 IO 顺序和同步到备 OSD 的 IO 顺序一致。当主备 OSD 同时完成写操作时，才向应用返回操作成功。

2．读修复（Read Repair）

Read Repair 机制是指在读数据失败时，系统会判断错误类型，如果是磁盘扇区读取错误，系统会自动从其他节点保存的副本读取数据，然后重新写入该副本数据到硬盘扇区错误的节点，从而保证数据副本总数不减少和副本间的数据一致性。

 

![image-20200909005820233](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909005820233.png)





## 12-FusionStorage的视图

![image-20200909005900968](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909005900968.png)

IO View：partition主和osd节点的映射关系

Partition View：partition主备对应的osd关系，ioview是partitionview的子集

这些视图作为元数据被MDC所管理，存放在两个位置：

- ZK管理的ZK分区中

- MDC管理的内存中

- 注：VBS和OSD保存部分元数据：VBS保存IO View；OSD保存Partion View

  

## 13-FusionStorage技术指标一览表

| 分类                                           | 指标名称                                                    | 指标值                                                       |
| ---------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| 集群                                           | 单集群存储服务器数量                                        | 4,096个                                                      |
| 单集群硬盘数量                                 | 49,152个                                                    |                                                              |
| 单集群支持的计算节点数量                       | 10,240个                                                    |                                                              |
| 单集群最大资源池数量                           | 128个                                                       |                                                              |
| 资源池                                         | 单资源池的硬盘数量                                          | 两副本（HDD或SSD）：12块～96块三副本（HDD或SSD）：12块～2,048块 |
| 单资源池的存储服务器数量                       | 两副本（HDD或SSD）：3台～16台三副本（HDD或SSD）：4台～256台 |                                                              |
| 单资源池的机柜数量                             | 非跨机柜数据安全：1个～12个跨机柜数据安全：4个～12个        |                                                              |
| 单个服务器最多可划分的资源池数量               | 1个                                                         |                                                              |
| iSCSI接口协议                                  | iSCSI CHAP用户最大数量                                      | Incoming用户：128个Outgoing用户：1,024个                     |
| iSCSI卷最大扩容容量                            | 256TB                                                       |                                                              |
| 卷                                             | 集群最大卷数量                                              | 1,280,000个                                                  |
| 单资源池最大卷数量                             | 640,000个                                                   |                                                              |
| 卷容量                                         | 64MB～256TB                                                 |                                                              |
| 卷最大共享主机数量                             | 128个                                                       |                                                              |
| 每个主机最大挂载卷数量                         | 512个                                                       |                                                              |
| 共享卷最大数量                                 | 20,000个                                                    |                                                              |
| 单个卷最大快照数量                             | 639,999个                                                   |                                                              |
| 单个卷最大链接克隆数量                         | 2,048个                                                     |                                                              |
| 同步复制卷的最大数量                           | 4,096个                                                     |                                                              |
| 同步复制卷的最大容量                           | 2TB                                                         |                                                              |
| 每个主机支持的同步复制卷的总容量               | 64TB                                                        |                                                              |
| 集群最大加密卷数量（不包含加密卷所创建的快照） | 640000个                                                    |                                                              |

## 14-FusionStorage中ZK的作用

zookeeper作为一个开源的分布式应用协调系统，用来完成统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等工作。

**MDC****主备管理**： MDC采用一主两备部署模式；在MDC模块进程启动后，各个MDC进程会向ZK注册选主，先注册的为主MDC；运行过程中，ZK记录MDC主备信息，并通过心跳机制监控MDC主备健康状况，一旦主MDC进程故障，会触发MDC重先选主

**数据存储**：在MDC运行过程中，会生成各种控制视图信息，包括目标视图、中间视图、IO视图信息等，这些信息的保存、更新、查询、删除操作都通过ZK提供的接口实现

**数据同步**：数据更新到主ZK，由主ZK自动同步到两个备ZK，保证主备ZK数据实时同步。一旦ZK发生主备切换，业务不受影响

## 15-FusionStorage中ZK的数量和MDC的数量

- ZK数量：3/5/7， 奇数原因：因为zookeeper 选举leader机制，n+1>n 总节点数：2n+1 控制集群创建完成后，数量不可更改。

- MDC数量（3~96个）：默认MDC数量为3个（创建MDC集群时与ZK数量一致），MDC数量会随着存储池的数量上升而自动扩展。MDC数量=ZK数量+存储池数量。MDC会管理存储池，一个MDC最多管理2个。

- MDC可以坏多少个？

  ​	MDC可以坏的数量=MDC总数据-1，即最终系统只有一个MDC仍可保持正常即可。

- ZK可以坏多少个？

  如果有2n+1个ZK；则可以坏n个；只要剩余的ZK超过总数的一般即可(保证ZK能够正常选举)。

## 16-FusionStorage中VBS模块介绍

![image-20200909012331276](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909012331276.png)

1.VBS的SCSI Initator启动将数据转发到SCSI Target。

2.SCSI Target接收到数据后将SCSI消息发送到VBP模块，VBP模块将SCSI类型的数据转写为key-value的形式。

3.VBP将key-value转发转给client，client对key-value执行哈希计算。

4.VBP与VBM进行通信，VBM负责管理元数据，然后通过Agent发送给FSM；VBM也可以与VBS_CLi进行通信，开放修改VBS的命令行接口；VBM通过Heart Beat进行通信，通过心跳将元数据消息同步给MDC，MDC将消息交给ZK存到ZK盘或者ZK分区

## 17-FusionStorage中SCSI和iSCSI的区别

### 部署方式：

SCSCI需要VBS安装在主机上；将卷挂载给VBS

ISCSI需要VBS部署在其他节点