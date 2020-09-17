---
title: "OpenStack  分享篇10 Neutron 上"
date: 2020-07-17T13:19:22+08:00
draft: true
---

## 前言：

与存储资源，计算资源一样。网络也是Openstack所管理的最重要的资源之一。Nova实现了OpenStack虚拟机世界的抽象，Swift与Cinder为虚拟机提供了安身之本，但是没有网络，任何虚拟机都将只是这个世界中的孤岛，不知道自己生存的价值。

最初，OpenStack中的网络服务由Nova中的一个单独模块nova-network来提供，但是为了提供更为丰富的拓扑结构，支持更多的网络类型，具有更高扩展性，Nova-network从Nova中独立出来这就是现在的Neutron。

当前Neutron已经成为OpenStack三大核心(存储，计算，网络)之一，对外提供NaaS（Network as a Service）服务。Neutron由Nova-network发展而来。Neutron的发展简史可以概括为三个阶段：Nova-network；Quantum，Neutron。

Nova-network阶段，其支持的主要功能有：

1）IP地址分配：包含为虚拟机分配私有（固定）和浮动IP地址

2）网络管理：仅支持三种网络，扁平网络，带DHCP功能的扁平网络，VLAN网络

3）安全控制：主要通过ebtables和iptables来实现

此阶段，Nova-network支持的功能比较简单，到了Quantum阶段，其支持的主要功能有：

1）支持多租户隔离，并提供面向租户的API

2）插件式结构支持多种网络后端技术，包括Open vSwitch，Cisco，Linux Bridge，Nicira NVP，Ryu，NEC等

3）支持位于不同二层网络的IP地址重叠

4）支持基本的三层转发和多路由器

5）支持隧道技术（Tuneling）

6）支持三层代理和DHCP代理的多借点部署，增强可扩展性和可靠性

7）支持负载均衡API

Quantum阶段所支持的功能已经初具规模。有了Quantum打下的良好基础，进入第三阶段之后，Neutron所支持的功能和应用场景，得到了更大规模的发展

![image-20200716101248194](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200716101248194.png)

### Neutron的体系机构：

类似于各个计算节点在Nova中被抽象成计算资源池，OpenStack所在整个屋里网络在Neutron中也被抽象成网络资源池，通过对屋里网络资源的灵活划分与管理，Neutron能够为同一屋里网路上的每个项目提供独立的虚拟网络环境。

我们在OpenStack云环境中基于Neutron构建自己私有网络的过程，就是创建各种Neutron资源对象并进行对接的过程，完全类似于使用真实物理网络设备来规划自己的网络环境。

![image-20200716104735465](https://gitee.com/hanstack/hanstack_image/raw/master/image/图7-1 典型Neutron网络结构.png)

1）首先至少要有一个由管理员创建的外部网络对象来负责OpenStack与Internet的连接，然后项目可以创建自己的私有内部网络并在其中创建虚拟机，为了使内部网络中的虚拟机能够访问互联网必须创建一个路由器将内部网络连接到外部网络。

​	在这个过程中Neutron提供了一个L3（三层）的抽象router和一个L2（二层）的抽象network，router对应物理网络环境中的路由器，为用户提供路由，NAT等服务，network则对应于一个真实物理网络中的二层局域网（LAN），从项目的角度看，它为项目私有。

这里的subnet从Neutron的实现上来看并不能完全理解为物理网络中子网的概念，subnet属于网络中3层概念，指定一段IPv4或IPv6地址并描述其相关的配置信息，它附加在一个二层network上指定属于这个network的虚拟机可使用的IP地址范围。一个network可以同时拥有一个IPv4 subnet和一个IPv6 subnet，除此之外，即使我们配置多个subnet，也并能够工作。

### Linux虚拟网络

Neutron最为核心的工作室对二层物理网络network的抽象与管理。在一个传统的物理网络里，可能有一组物理的Server，上面分别运行有各种各样的应用，比如web服务，数据库服务等。为了彼此之间能够相互通信，每个物理Server都用于一个或多个物理网卡（NIC），这些NIC被连接在物理交换设备上，比如交换机（Switch）

![image-20200716111904931](https://gitee.com/hanstack/hanstack_image/raw/master/image/传统二层物理网络.png)

虚拟化技术被引入之后，上述的多个操作系统和应用可以以虚拟机的形式分享同一物理Server，虚拟机的生成与管理由Hypervisor（或VMM）来完成。

![image-20200716112649481](https://gitee.com/hanstack/hanstack_image/raw/master/image/虚拟网络结构.png)

虚拟机的网络功能由虚拟网卡（vNIC）提供，Hypervisor可以为每个虚拟机创建一个或者多个vNIC，站在虚拟机的角度，这些vNIC等同于物理的网卡。为了实现与传统物理网络等同的网络结构，与NIC一样Switch也被虚拟化为虚拟交换机（vSwitch），各个vNIC连接在vSwitch的端口上，最后这些vSwitch通过物理Server上的物理网卡（NIC）访问外部的物理网络。

对于一个虚拟的二层网络结构来说，主要是完成两种网络设备的虚拟化：NIC硬件与交换设备。Linux环境下网络设备设备的虚拟化主要由以下几种形式。Neutron也是基于这些技术来完场项目私有网络network的构建。

Neutron在构建网络服务时，利用了很多Linux虚拟网络的功能。

1）TAP/TUN/VETH

TAP/TUN是linux的内核实现的一对虚拟网络设备，TAP是工作在二层，TUN工作在三层，Linux内核通过TAP/TUN设备向绑定该设备的用户空间程序发送数据，反之，用户空间程序也可以像操作硬件网络设备那样通过TAP/TUN设备发送数据。

（注：在Linux中的设备即Device并不是生活中的设备，在这的里的设备通常指的是一个类似于数据结构，内核模块或者设备驱动这样的概念）

```
struct tun_struct{
	char name(8)
	unsigned long flags;
	struct fasync_struct *fasync;
	wait_queue_head_t read_wait;
	struct net_device dev;
	struct sk_buff_head txq;
		struct net_device_stats;
}
```

基于TAP驱动，能够实现虚拟网卡的功能，虚拟机的每个vNIC都会与Hypervisor中的一个TAP设备相连。当一个TAP设备被创建时，在Linux涉笔文件目录下会生成一个对应的字符设备文件，用户程序可以向打开普通文件一样打开这个文件进行读写。

当这个TAP设备文件执行wirte()操作时，对于Linux网络子系统来说，就相当于TAP设备收到了数据，并请求内核接受它，Linux内核收到此数据后将根据网络配置进行后续处理，处理过程类似于普通的物理网卡从警外界接受数据。当用户程序执行read()请求时，相当于向内核查询TAP设备上是否有数据需要被发送，有的话取出到用户程序里，从而完成TAP设备发送数据的功能。在这个过程里，TAP设备可以被当作是本机的一个网卡，而操作TAP设备的应用程序相当于另一台计算机，他通过read/write系统调用，和本机进行网络通信，Subnet数据网络中的3曾概念，指定一段IPv4或者Ipv6地址并描述相关的配置信息。他们附加在一个二层network上，并指明数据这个network的虚拟机可使用的IP地址范围。

TAP从功能定位上来讲，位于数据链路层，数据链路层的主要协议有：

​	1）点对点协议（point-to-point protocol）

​	2）以太网（Ethernet）

​	3）高级数据链路协议（Hight-Level Data Link Protocol）

​	4）帧中继（Frame Relay）

​	5）异步传输模式（Asynchronous Transfer Mode）

![](https://gitee.com/hanstack/hanstack_image/raw/master/image/tun1tun2.png)

但是TAP只是与其中一种协议以太网（Ethernet）协议对应。所以，TAP有时也称为“虚拟以太设备”

2）namespace

namespace是Linux虚拟网络的一个重要概念。传统的Linux的许多资源时全局的，比如进程ID资源。而那么space的目的首先就是将这些资源做隔离。Linux可以在一个Host内创建许多namespace,于是那些原本是全局的资源，就变成了namespace范围内的全局资源。而不同的namespace的资源互相不可见，彼此透明

单纯从网络视角看，一个namespace提供了一份独立的网络协议栈（网络设备接口，IPv4，IPv6，IP路由，防火墙规则，sockets等）一个设备（linuxDevice）只能位于一个那么space中，不同那么space中的设备可以通过veth pair进行桥接。



3）veth pair

veth pair不是一个设备而是一对设备，用来连接两个虚拟以太网端口。操作veth pair，需要跟namespace一起配合。

4）bridge：网桥，Linux中用于标识一个能连接不同网络设备的虚拟设备，Linux中传统实现的网桥类似于一个hub设备，而ovs管理的网桥一般类似于交换机。

linux bridge是工作在L2（二层）的虚拟网络设备，功能类似于物理交换机。Bridge可以绑定其他Linux设备作为从设备，并将这些设备从设备虚拟化为端口，当一个设备被绑定到Bridge上时，就相当于在真实网络中的交换机端口插入了一个连接有终端的网线。

![image-20200717003112388](https://gitee.com/hanstack/hanstack_image/raw/master/image/LinuxBridge.png)

Bridge设备br0绑定了实际设备eth0与虚拟机设备tap0/tap1,此时，对于hypervisor的网络协议栈的上层来说，只看到br0，并不会关心桥接细节。当从这些设备收到数据包时，会将其提交给br0决定数据包的去向。br0会根据MAC地址与端口的映射关系进行转发（MAC地址表）

因为Bridge工作在第二层，所以绑定在br0上的设备eth0,tap0,tap1均不需要再设置IP地址，对上层的路由器来说他们都位于同一个子网，因此只需为br0设置IP。

5）br-int：bridge-intergration，综合/集成网桥。常用语表示实现主要内部网络功能的网桥。在逻辑概念上，他是跨越整个OpenStack的内部大网桥：所有的Port（虚拟机，虚拟路由器，DHCP等等）都直接连接在br-int上，通过不同的VLAN tag来区分不同的Subnet，做到了网络之间的隔离。

注：将分布在各个物理机上的br-int网桥联通，实现逻辑上统一的集成网桥，Neutron可以采用三种方案来完成这一任务：VLAN，GRE和VXLAN，分别对用三种Tenant Network类型。每一个用户创建的Tenant Network根据其类型决定内部网络采用什么样的方式来括约物理机的界限。

6）br-ex：bridge-external，外部网桥，通常表示负责跟外部网络通信的网桥。

7）GRE：General Routing Encapsulation，一种通过封装来实现的隧道的方式。在opensatck中一般是基于L3的gre。即originna pkt/GRE/IP/ethernet

8）iptables：Linux上常见的实现安全策略的防火墙软件

9）VxLAN：利用UDP协议作为底层传输协议的OverLay实现。一般认为作为VLAN技术的延伸或者替代技术

#### Neutron的三类节点

![](https://gitee.com/hanstack/hanstack_image/raw/master/image/neutorn的三类节点9.png)

##### 计算节点

计算节点（Compute Node）最直观，计算是OpenStack的三大组件之一（计算，存储，网络）。计算主要指将服务器虚拟化，在服务器上创建VM。一般来说一个计算节点即一个Host上有多个VM存在。

在计算节点里面还有Bridge。这里的Bridge指的是虚拟的Bridge。不同Host的VM之间的二层通信，通过计算节点的Bridge就可以完成

##### 网络节点

在Host内部的VM想要访问Internet就必须通过Router到达数据中心的网关，再从网关与Internet完成通信。Router也是Linux虚拟出来的，Router所在的节点就是网络节点。网络节点上还部署着DHCP等网络服务。一个计算节点通常指的是一个Host。一个网络几点通常意味着一个Host或者多个Host因为不是所有的网络服务都必须部署在一个节点上，通常来说网络服务会分别部署在多个Host上。

##### 控制节点

Neutron一方面在各个计算节点和网络节点上运行各种Agent，另一方面Neutron的各种服务运行在一个或者多个控制节点上。

#### Neutron的网络

外部网络（Provider network）：提供上网服务，外界登录openstack。

管理网络(Management network) : 提供openstack组件之间的内部通信。 该网络内的IP地址只有数据中心内部可达。
数据网络(Data Network) : 提供云内部VM之间的数据通信。 该网络内的IP地址取决千使用的网络插件程序。
API网络(API Network) : 为租户提供包括Network API在内的所有openstack API。 该网络内的IP地址允许Internet上的任何人可达。
Provider Network网络：由管理员创建。是已经存储在的物理网络在Neutron中的映射。典型的例子就是将公网作为ProviderNetwork (通常是br-ex的映射）来创建ExternalNetwork。ExternalNetwork 是一种ProvdierNetwork, 典型地它是用于向虚拟机提供因特网访间。它的type智能是flat和vlan类型。常用的网络是Flat和vlan,vxlan基本使用不上。
Tenant网络：用户自己创建。 可以设置的选项就是：Flat, vlan和vxlan local主要是测试使用。

#### 计算节点的实现模型

一个基于openstack的云系统会有很多计算节点，一个计算节点就是一个Host。计算节点里存在多个VM，这些计算节点也是OpenStack存在的基础。但是VM不能像一个个孤岛一样存在，VM之间需要通信，且需要能够实现跨越Host实现Vm之间的通信。简单来说，通信分为二层通信和三层通信，二层通信使用Bridge（交换机）三层需要Router。Router在网络节点上。

![](https://gitee.com/hanstack/hanstack_image/raw/master/image/计算节点抽象模型.png)

在上图中两个主机位于一个机架内，两个主机分别虚拟出多个VM。每个Host内调出若干个虚拟机组成一个二层网络（在这里可以理解成为一个VLAN）。这样的一个场景需要的物理设备有：机架，主机，ToR交换机。

图中Security Layer一般由各种虚拟网元组成。Neutron不能管理物理网络。在计算节点的模型中Neutron管理的是虚拟网元，即Security Layer，Integration Layer这两层网元。这两层实现多种二层网络。

Neutron当前支持的二层网络类型有Loca，Flat，VLAN，GRE，VXLAN，Geneve

#### VLAN实现模型

![image-20200717012136227](https://gitee.com/hanstack/hanstack_image/raw/master/image/计算节点的VLAN模型.png)

##### qbr与br-int

在上图中的qbr-xxx/qbr-yyy一般简称为qbr。qbr是Quantum Bridge的缩写，而OpenStack网络组建的前一个名称就是Quantum。

br-int：表达的是Intergration Bridge

qbr与br-int都是Bridge（此前说过Linux中的Bridge指的是交换机）。qbr实现的载体是Linux Bridge。Br-int实现的载体是OVS（Open vSwitch）。

qbr与br-int之间通过veth pair相连。VM与qbr之间，通过tap相连接。

qbr与br-int都是bridge的一种表现形式，呢么为什么需要两层bridge呢？

​	1）如果只有一个qbr，qbr仅仅是一个Linux Bridge，他的功能不能满足实际场景的需求

​	2）如果只有一个br-int。由于br-int实际上一个OVS，而OVS并不支持基于iptables的安全组的功能。OpenStack是基于iptables规则来实现安全组功能。

所以OpenStack中引入qbr最主要的目的是使用qbr提供的iptables实现security group功能（qbr 有时候也会被称为安全网桥）

##### br-ethx

br-ethx也是一个Bridge。也是一个OVS。他的全称为Bridge-Ethernet-External。负责与外部网络进行通信。也就是Host与外部进行通信。br-ethx属于一个Network，这个Network由Neutron负责管理。br-ethx与br-int之间的接口是veth pair。在上图中br-ethx与br-ethx之间通信使用的实际的物理网卡进项通信的。这也是上图中唯一一对使用物理NIC进行通信的链路。

#### VM与VLAN ID 

上图中4个VM分别属于两个vlan。VM1和VM3属于vlan 100；VM 2和VM 4属于vlan 20。从外部的用户视角来看vm1和vm3同属一个vlan；vm2和vm4同属一个vlan。但是vm实际所处的host对外部的用户来说是透明的。也并不需要用户去关心。

接下来以VM1和VM3为例讲解流量走向。

1）VM1的vNIC从A口发出的是untag的报文

2）报文从B口进入qbr-xxx从C口发出此时依旧是untag的报文

3）报文从D口进入br-int打上vlan100 的tag，从E口发出；到F口将vlan ID转为vlan10

4）G口 携带vlan 10的tag到达对端的br-ethx

5）对端的br-ethx的H口接收到tag 10 的报文后通过I口发出将vlan ID转为10

6）报文携带tag为10的报文通过J口进入br-int。

7）br-int将报文从k发出，同时剥离tag 10的标签，还原成untag 报文发送给qbr-xxx

8）qbr-xxx将untag的报文发送给VM3

![image-20200717122236013](https://gitee.com/hanstack/hanstack_image/raw/master/image/流量走向.png)

