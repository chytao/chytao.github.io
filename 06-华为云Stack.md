## 00-FusionCloud6.3架构

![image-20200915020924701](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200915020924701.png)

| 功能分层 | 功能描述                                                     |
| -------- | ------------------------------------------------------------ |
| 基础设施 | 基础设施包括构建数据中心所需的服务器、存储设备和网络设备。基础设施层可根据不同业务的需求，提供多种类型的硬件部署架构。 |
| 资源池   | 资源池是基于物理基础设施构建的虚拟计算、虚拟存储和虚拟网络资源池。<br />FusionSphere OpenStack提供对虚拟计算、虚拟存储和虚拟网络的资源池化和管理能力，并提供资源池管理能力：<br />虚拟化池<br />裸金属服务器池<br />块存储池<br />文件存储池<br />网络资源池<br />容灾存储池<br />备份存储池<br />SAP Cloud资源池<br />FusionStage容器池（被FusionStage云服务调用）<br />**其他资源池：**<br />对象存储池：OBS云服务直接调用对象存储池（不经过FusionSphere OpenStack）。<br />大数据池：HDS、DBS、HDS_for_Cluster云服务直接调用大数据池（不经过FusionSphere OpenStack）。<br />两级云资源池：对端华为云Stack资源池接入本端。<br />管理混合云资源池：公有云资源通过API适配方式接入华为云Stack。<br />纳管VMware池：VMware云服务直接调用纳管VMware池（不经过FusionSphere OpenStack）。<br />纳管Hyper-V池：Hyper-V云服务直接调用纳管Hyper-V池（不经过FusionSphere OpenStack）。<br />纳管Power池：Power云服务直接调用纳管Power池（不经过FusionSphere OpenStack）。<br />虚拟化资源池（FusionManager）：华为云Stack统一管理FusionManager管理的虚拟化资源池，同步FusionManager管理的虚拟化资源池的弹性云服务器、云硬盘等云服务资源。<br />虚拟化资源池（FusionCompute）：华为云Stack统一管理FusionCompute管理的虚拟化资源池，同步FusionCompute管理的虚拟化资源池的弹性云服务器、云硬盘等云服务资源。 |
| 管理域   | 管理域由ManageOne提供对多个云数据中心的统一管理调度能力：<br />运营管理：ManageOne运营面提供对云服务的统一运营能力，提升运营操作的敏捷性，提升业务运营效率。<br />运维管理：ManageOne运维面提供对虚拟资源和物理资源的统一运维能力，提升运维操作效率。 |
| 云服务   | 云服务统一管理多个数据中心资源池层提供的资源，公共组件为云服务提供公共能力，例如提供统一的操作系统EulerOS。 |
| 应用域   | 由第三方提供应用，基于华为云Stack提供的云服务，构建用户的业务系统，满足各行业用户业务需求。 |

## 02-FusionSphere6.3基础公共组件

### 存储服务

- [EVS](https://support.huaweicloud.com/evs/index.html)：EVS（Elastic Volume Service），即云硬盘，是一种虚拟块存储服务，主要为ECS（Elastic Cloud Server）和BMS（Bare Metal Server）提供块存储空间。用户可以在线创建云硬盘并挂载给实例，云硬盘的使用方式与传统服务器硬盘完全一致。同时，云硬盘具有更高的数据可靠性，更高的I/O吞吐能力和更加简单易用等特点，适用于文件系统、数据库或者其他需要块存储设备的系统软件或应用。

- [OBS](https://support.huaweicloud.com/obs/index.html)：OBS（Object Storage Service），即对象存储服务，是一个基于对象的海量存储服务，为客户提供海量、安全、高可靠、低成本的数据存储能力，包括创建、删除桶，上传、下载、删除对象等。OBS适合任意类型的文件，适合普通用户、网站、企业和开发者使用。

  FusionStorageOBS，表示对象存储为FusionStorage OBS 7.0版本，用作OBS后端存储设备，提供海量、弹性可扩展、高可靠的对象存储服务。

- [SFS](https://support.huaweicloud.com/sfs/index.html)：SFS（Scalable File Service），即弹性文件服务，为用户的弹性云服务器（ECS）提供一个完全托管的共享文件存储，符合标准文件协议（NFS和CIFS），能够弹性伸缩至PB规模，具备可扩展的性能，为海量数据、高带宽型应用提供有力支持。

  SFS-DJ，即OceanStor DJ（Manila），作为SFS服务端，接收SFS管理控制台请求。

### 网络服务

- [VPC](https://support.huaweicloud.com/vpc/index.html):VPC（Virtual Private Cloud），即虚拟私有云，是一套为实例构建的逻辑隔离的、由用户自主配置和管理的虚拟网络环境，旨在提升用户资源的安全性，简化用户的网络部署。

  用户可以在VPC中自由选择IP地址范围、创建多个子网、自定义安全组以及配置路由表和网关等，方便地管理和配置网络，进行安全、快捷的网络变更。同时，通过自定义安全组内与组间实例的访问规则以及防火墙等多种安全层，加强对子网中实例的访问控制。

  SNAT（源地址转换）将VPC内的一段网段内的IP地址映射成为公网IP地址，它主要是为内部云服务器提供访问Internet的通道。()

- [EIP](https://support.huaweicloud.com/eip/index.html):EIP（Elastic IP），即弹性IP，是可以通过云平台以外的网络直接访问云平台上业务的IP地址。EIP是一个静态的公共IP地址，可以与弹性云服务器、裸金属服务器、虚拟IP、弹性负载均衡等资源灵活地绑定及解绑 。

  EIP-QoS是EIP外部网络流量限速特性。安装该特性后，可在ManageOne运营面对用户的EIP带宽进行调整()

- [ELB](https://support.huaweicloud.com/elb/index.html):ELB（Elastic Load Balance），即弹性负载均衡，是将访问流量根据转发策略分发到后端多台弹性云服务器的流量分发控制服务。弹性负载均衡可以通过流量分发扩展应用系统对外的服务能力，实现更高水平的应用程序容错性能。弹性负载均衡可以消除单点故障，提高整个系统的可用性。

- VFW:VFW（Virtual Firewall），即虚拟防火墙，是虚拟私有云的安全服务，对子网或VPC进行访问控制，支持黑白名单（即允许和拒绝策略），根据与子网或VPC关联的入方向/出方向ACL（Access Control List）规则，判断数据包是否被允许流入/流出子网或VPC。

- [VPN](https://support.huaweicloud.com/vpn/index.html):VPN（Virtual Private Network），即虚拟专用网络，用于在远端用户和虚拟私有云之间建立一条安全加密的通信隧道。当您作为远端用户需要访问VPC的业务资源时，您可以通过VPN连通VPC。

  VPN-QoS是VPN外部网络流量限速特性。安装该特性后，可在ManageOne运营面对用户的VPN带宽进行调整。

- SG: SG（Security Group），即安全组，是一个逻辑上的分组，为同一个VPC内具有相同安全保护需求并相互信任的实例提供访问策略。安全组创建后，用户可以在安全组中定义各种访问规则，当服务器加入该安全组后，即受到这些访问规则的保护。默认安全组中的默认规则是在出方向上的数据报文全部放行，安全组内的服务器无需添加规则即可互相访问。一个服务器可以加入多个安全组，且与每个安全组内的服务器都可以互相访问。

-  [Direct Connect](https://support.huaweicloud.com/dc/index.html):云专线（Direct Connect），用于搭建用户本地数据中心与云上虚拟私有云之间高速、低时延、稳定安全的专属连接通道。用户可以通过云专线将用户侧的用户网络、数据中心、主机托管区连接至云上VPC专线连接，享受高性能、低延迟、安全专用的数据网络。

- CFW:CFW（Cloud Firewall），即云防火墙，其分散在每一台VM前直接提供防护，因此能进行最细粒度的微隔离访问控制。在流量可视化的辅助下，可提供基于用户业务语言的安全策略配置能力。

### 计算服务

- [ECS](https://support.huaweicloud.com/ecs/index.html):ECS（Elastic Cloud Server），即弹性云服务器，是由CPU、内存、镜像和云硬盘组成的一种可随时获取、弹性可扩展的计算服务器，同时结合虚拟私有云、虚拟防火墙和云服务器备份等云服务，为用户打造一个高效、可靠和安全的计算环境，确保用户的服务持久稳定运行。

- [BMS](https://support.huaweicloud.com/bms/index.html):BMS（Bare Metal Server），即裸金属服务器，为租户提供专属的物理服务器，拥有卓越的计算性能，能够同时满足核心应用场景对高性能及稳定性的需求，并且可以和虚拟私有云等其他云服务灵活的结合使用，综合了传统托管主机的稳定性与云上资源高度弹性的优势。

- [IMS](https://support.huaweicloud.com/ims/index.html):IMS（Image Management Service），即镜像服务，是一个包含了软件及必要配置的弹性云服务器模板，至少包含操作系统，还可以包含应用软件（例如，数据库软件）和私有软件。镜像分为公共镜像、私有镜像和共享镜像。用户可以灵活便捷的使用公共镜像、私有镜像或共享镜像申请弹性云服务器。同时，用户还能通过弹性云服务器或外部镜像文件创建私有镜像。

- [AS](https://support.huaweicloud.com/as/index.html):AS（Auto Scaling），即弹性伸缩，是根据用户的业务需求，通过策略自动调整其业务资源。您可以根据业务需求自行定义伸缩配置和伸缩策略，降低人为反复调整资源以应对业务变化和高峰压力的工作量，帮助您节约资源和人力成本。

- SAP HANA: SAP HANA解决方案基于华为云Stack提供运行SAP应用和SAP HANA的云资源池，支持SAP HANA云服务器和SAP HANA裸金属服务器两种形态，满足高性能、超高IO、大CPU/内存规格要求。

  

## 03-云硬盘和本地硬盘

[EVS](https://support.huaweicloud.com/evs/index.html)（Elastic Volume Service），即云硬盘，是一种虚拟块存储服务，主要为ECS（Elastic Cloud Server）和BMS（Bare Metal Server）提供块存储空间。用户可以在线创建云硬盘并挂载给实例，云硬盘的使用方式与传统服务器硬盘完全一致。同时，云硬盘具有更高的数据可靠性，更高的I/O吞吐能力和更加简单易用等特点，适用于文件系统、数据库或者其他需要块存储设备的系统软件或应用。

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/zh-cn_image_0194995175.png)

本地磁盘：也成本地盘，是指挂载在实例所在的物理机（宿主机）上的本地磁盘，是一种临时块存储。

### 图说EVS

![1595830382648000612](https://gitee.com/hanstack/hanstack_image/raw/master/image/1595830382648000612.gif)

两者区别：
1. 性能：云硬盘具有更高的I/O吞吐能力。本地磁盘的性能与物理主机的负载情况有关。
2. 可靠性：云硬盘可靠性高，本地磁盘存在单点故障风险。
3. 受VDC配额的限制：云硬盘的容量和数量受VDC配额的限制，支持统计使用量。本地磁盘的容量和数量不受VDC配额限制，也无法统计使用量。
4. 生命周期：云硬盘始终不受实例运行时间的影响，可以将磁盘从实例中卸载，并将其挂载到另一个实例。
5. 特性支持：云硬盘支持热迁移、磁盘升降配置、快照等。
    磁盘模式：根据是否支持高级的SCSI命令来划分云硬盘的磁盘模式，分为VBD （Virtual Block Device）类型和SCSI （Small Computer System Interface）类型。
  1. VBD类型：VBD类型的云硬盘只支持简单的SCSI读写命令。
  2. SCSI类型：SCSI类型的云硬盘支持SCSI指令透传，允许弹性云服务器操作系统直接访问底层存储介质。除了简单的SCSI读写命令，SCSI类型的云硬盘还可以支持更高级的SCSI命令。
6. 磁盘类型：磁盘类型是在创建磁盘时可供选择的类型或标签，一个磁盘类型对应一组磁盘所使用的后端存储。用户可以根据接入的不同后端存储类型划分云硬盘的磁盘类型，以便满足业务不同性能要求。
    根据磁盘所使用的后端存储的性能差异，推荐的典型磁盘类型和应用场景如下：
  1. 普通性能：适用于大容量、读写速率要求不高、事务性处理较少的应用场景，例如部署开发测试应用程序等。
  2. 中等性能：适用于对性能要求不高，但是要求具有丰富的企业级特性场景，适用于普通数据库、应用VM、中间件VM。
  3. 高性能：适用于高性能，高读写速率要求，满足高带宽吞吐能力的应用场景，例如数据仓库。
  4. 超高性能：适用于对IO性能密度要求极高，数据密集型的场景，例如NoSQL/关系型数据库。

## 04-什么是镜像



### [什么是镜像](https://support.huaweicloud.com/ims/index.html)

镜像是一个包含了软件及必要配置的弹性云服务器模板，包含操作系统、预装的公共应用、用户的私有应用或用户的业务数据。镜像分为公共镜像、私有镜像和共享镜像。

镜像服务（Image Management Service，以下简称IMS）提供简单方便的镜像自助管理功能。用户可以灵活便捷的使用公共镜像、私有镜像或共享镜像申请弹性云服务器。同时，用户还能通过弹性云服务器或外部镜像文件创建私有镜像。

### 图说IMS

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/1549097108553705.gif)

## 05-HUAWEI CLOUD Stack Deploy安装华为云Stack

### 物理机部署

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/zh-cn_image_018255572.png)

### 虚拟机部署（支持VirtualBox部署）

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/zh-cn_image_0202314629.png)