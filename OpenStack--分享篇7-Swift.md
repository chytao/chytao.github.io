---
title: "OpenStack  分享篇7 Swift"
date: 2020-05-11T17:55:24+08:00
draft: true
---

## 什么是Swift

https://wiki.openstack.org/wiki/Swift

OpenStack的Swift项目最初来自RackSpace Cloud File项目，由RackSpace于2010年贡献给OpenStack；同Nova作为OpenStack最初的两个项目。

Swift是OpenStack中的对象存储项目，Swift作为对象存储的一种非常适合存储静态数据，就是那些在一定时期内更新频率比较低的数据。比如虚拟机的镜像，多媒体文件，和数据备份等。

Swift并不适合用于存放更新频率较为频繁的数据，如果要存储需要实时更新的数据一般可以选用Cinder进行存放。

Swift作为对象存储的一种，那么Swift负责管理的逻辑单元为对象（object）。传统的数据存储方式文件存储，在对存储的文件进行描述时一般采用的元数据加文件本身的方式，两者一般分开存放。Swift采用的Object对象元数据和内容一起存放。

## 为什么要使用Swift

Swift能够提供极高的数据持久性

Swift采用完全对称的架构，使得Swift中的各个节点可以完全对等，能够极大的降低成本

Swift拥有无限的可扩展性；Swift的性能能够实现线性提升

Swift无单点故障；Swift的元数据存储完全是随机分布的，并且与对象文件存储一样，元数据会存储多份，整个Swift集群中，没有一个角色是单点的，并且架构和设计上保证无单点业务是有效的。

简单；可依赖

Swift最为OpenSatck项目的一部分是免费且开源的

Swift可以用于独立的存储系统或者云计算环境的一部分（云计算环境和独立存储系统在使用认证服务上有所不同）

Swift采用多副本机制；保证数据的可靠性

## Swift的体系结构

https://www.swiftstack.com/docs/introduction/openstack_swift.html

![568099338778041596](https://gitee.com/hanstack/hanstack_image/raw/master/image/568099338778041596.png)

Swift在架构上可以划分为两个层次，一个是Access Tier即访问层；一个是Storage Nodes即存储层。

--https://www.swiftstack.com/docs/introduction/openstack_swift.html

### Access Tier

访问层包含两个组成部分一个是Authenication负责认证，一个是proxy node 代理服务节点负责RESTful的请求。

Poxy上运行有Poxy Server用于处理用户的RESTful请求消息，当Poxy接收到来自用户的RESTful请求以后会将请求消息传递给Authenication进行认证处理。

发送给Swift的请求至少包含三个组成部分

​	-HTTP verb（例如：get（获取），DELL（删除）；PUT（上传））

​	-Authenication 信息

​	-Storage URL：存储地址的URL

​	-Optional（可选项）：写入数据和元数据

HTTP verb：提供具体的请求操作。例如将一个object对象放入集群中，从集群中获取账户（特别注意Swift中的账户信息指的并不是用户账户而是存储的区域信息）信息。

Swift中对象的存储URL如下所示：

https://swift.example.com/v1/account/container/object

URL是由两部分组成的：

1.存储集群的位置：swift.example.com/v1

2.存储的位置：account/container/object

![576950383606996326](https://gitee.com/hanstack/hanstack_image/raw/master/image/576950383606996326.png)

#### account（账户）：

账户存储位置是一个唯一的命名存储区域，其中包含账户本身的元数据信息和账户中容器的列表。

#### /account/container(容器):

容器存储位置使账户中定义的存储区域，存储容器的元数据和容器中的对象列表

#### /account/container/object(对象)：

对象存储位置负责存放数据对象和对象的元数据信息



### Storage Nodes

存储层是由一系列的存储节点组成的，负责对象数据的存储。Proxy Node接收到来自用户的访问请求时会将其转发到相应的存储节点上。为了提高系统的可靠性，在系统出现故障的时候将故障隔离在最小的物理范围之内，存储层在物理上又进行了一些划分。

​	Regina：在地理上隔绝的区域，不同Regina通常所处的地理位置相距较远

![545962553161979187](https://gitee.com/hanstack/hanstack_image/raw/master/image/545962553161979187.png)

​	Zone：在每个Regina内部进一步进行划分，每个Zone一般代表一个独立的存储节点，可以是一个机柜。

![814769980415337893](https://gitee.com/hanstack/hanstack_image/raw/master/image/814769980415337893.png)

​	Storage Node：存储对象的物理节点，通常可以将其理解为一个物理的存储服务器

​	Device：简单理解为一个磁盘即可

​	partition:处在Device上的文件系统中的目录。

![733922377504948253](https://gitee.com/hanstack/hanstack_image/raw/master/image/733922377504948253.png)

Swift的四个服务器进程分别是Proxy，account，container和object，当一个节点上只有代理进程运行时，此节点称之为代理节点；当一个节点上运行一个或多个服务进程时（account，container，object）时，该节点通常称之为存储节点。

### Swift如何保证数据的一致性

Storage Node上存在一个三层结构 Account;Container;object.与之相对应的是Storage Node上运行的三种服务。

Account Server：提供Account相关的服务，包含Container列表和account的元数据信息。数据存放在SQLlite的数据库中。

Container Server：提供Container相关的服务，包含Object列表和Container的元数据信息。数据存放在SQLlite的数据库中。

Object Server：提供Object相关的服务，包含Object对象和Object的元数据。

为例提高数据的可靠性避免单节点的物理故障造成数据丢失或损坏，Swift为每个Object对象建立了一定数量的副本，通过副本机制来实现数据的可靠性。默认是3副本。并且每个副本存放在不同的Zone中，这样即使发生某个Zone损坏，那么也能使用其他Zone上的数据接续提供可持续的数据服务。

在Swift中数据的副本是以partition为单位进行存放的。也就是说对象副本实际上由partition副本组织在一起用partition副本间接来实现Object副本的。

既然Swift使用了三副本机制，副本之间的数据就必须保持一致，也就是说有一个副本产生数据更新的话，其他副本也要进行更新，使得副本之间的数据时刻保持一致。

#### Swift保持数据一致性的服务

Auditor：通过持续扫描磁盘检查Account；Containe和Object的完整性，如果发生现有数据损坏那么Auditor就对损坏的数据进行隔离，然后使用Replication从其他的节点上获取对应的副本用来恢复本地数据。

Updater：在创建一个Container时包含该Container的数据的Account需要进行数据的更新，将更新消息发送到SQLlite的Account数据库中；同样的在创建一个Object对象是Container同样需要将更新消息发送到SQLlite的Container数据库中。但是假如Account server和Container业务繁忙，导致数据更新不成功，此时就需要Updater继续更新。

Replication：负责检测各个节点上的数据及副本是否一致，发现不一致时将副本更新至最新版本。

#### 实现对象和物理位置之间的映射--Ring

Ring记录了存储对象和物理位置之间的映射关系，Account；Container；Object都有自己的Ring.Proxy Server接收到用户的请求时会根据不同的操作实体（Account；Container；Object）分别找到各自对应的Ring，来确定他们在服务器集群中的具体位置。而Account；Container；Object在Ring中的位置也是交给Proxy Server进行维护的。

Ring通过Zone，Device，Partition和Replica的概念来维护映射信息。每个Partition的位置关系有Ring来进行维护。

#### 引入Ring之后Swift logic Architectural



![792003611753423029](https://gitee.com/hanstack/hanstack_image/raw/master/image/792003611753423029.png)

Swift通过Ring来实现对物理节点的管理，包括记录对象与物理存储位置的映射，物理节点的添加和删除等。

#### 基本原理：一致性散列（Consisten Hashing）

面对海量级别的对象，需要存放在成千上万台服务器和硬盘设备上，首先要解决寻址问题，如何将这些对象分不到这些设备的地址上。Swift是基于一致性散列技术，通过计算可以将对象均匀的分布在虚拟空间的虚拟节点上，在增加或者删除节点是可大大减少移动的数据量；虚拟空间大小通常采用2的n次幂，便于进行高效的移位操作，然后通过独特的数据结构Ring再将虚拟节点映射到实际的物理存储设备上，完成寻址过程。

通常我们使用Hash算法，首先计算出对象的Hash值Key，然后将key进行取模（key%N N代表的是partition的个数）也做key mod N；计算完成后得到的余数的结果便是数据存放的节点。比如N等于3；且经过计算出来的，key值分别为0,1,2,3,4,5,6对N进行取模得到的结果分别为0,1,2,0,1,2.,0也就是将Object分别存放在0,1,2,1,2,0节点上。在这个例子当中每个Storage Node上都存放了两个Object。如果Hash算法是平均，那么数据将会存放在3个节点当中。如果每个数据的访问量比较平均那么数据的负载将会比较平均。

在实际使用过程中，由于数据的访问量和使用量进一步增加就需要针对节点进行扩容，以增强数据的访问请求。

如果我们将节点增加到4个，那么此时N为4;执行为key mod N的操作得出的结果为0,1,2,3,1,1,2跟上面的N为3时计算结果进行对比。得出Hash值为3,4,5,6的Object需要进行重新分配，如果存储节点的数量以及存储节点上的数据很大时迁移的成本就会很高。为了减少节点增减所带来的成本，Swift使用的是一致性Hash算法，在存储节点数量发生变化时，经历而为的减少已经存在的对象和节点之间的映射关系。

| key  | N为3 | N为4 |
| ---- | ---- | ---- |
| 0    | 0    | 0    |
| 1    | 1    | 1    |
| 2    | 2    | 2    |
| 3    | 0    | 3    |
| 4    | 1    | 0    |
| 5    | 2    | 1    |
| 6    | 0    | 2    |





#### 一致性Hash的过程分为以下几个步骤

计算每个对象的Hash值并且将他们均匀的分布到一个虚拟空间上去，一般用2^32标识该虚拟空间。

假设有2^m个存储节点，那么将虚拟空间均匀的分成2^m等份，每份长度为2^(32-m)

假设一个对象名称Hash值key为n；那么该对象对应的存储节点为 n/2^(32-m);转换为二进制移位操作就是讲Hash之后的结果右移32-m位。

以m = 3为例，描述一下具体过程。

存储节点的个数为2^m 为8个虚拟存储节点。

![403451260661319628](https://gitee.com/hanstack/hanstack_image/raw/master/image/403451260661319628.png)

#### 数据一致性模型（Consistency Modle）

按照Rric Brewer的CAP（Consistency，Availability，Partition Tolerance）理论，无法同时满足三个方面，Swift放弃严格一致性（满足ACID事务级别），而采用最终一致性模型（Eventual Consistency），来达到高可用性和无线水平扩展能力。为了实现这一个目标，Swift采用Quorum仲裁协议（Quorum有法定投票人数的含义）

（1）定义：N：数据的副本总数；W：写操作被确认接受的副本数量；R：读操作的副本数量

（2）强一致性：R+W>N，以保证对副本的读写操作会产生交集，从而保证可以读取到最新版本；如果 W=N，R=1，则需要全部更新，适合大量读少量写操作场景下的强一致性；如果 R=N，W=1，则只更新一个副本，通过读取全部副本来得到最新版本，适合大量写少量读场景下的强一致性。

（3）弱一致性：R+W<=N，如果读写操作的副本集合不产生交集，就可能会读到脏数据；适合对一致性要求比较低的场景。

Swift 针对的是读写都比较频繁的场景，所以采用了比较折中的策略，即写操作需要满足至少一半以上成功 W >N/2，再保证读操作与写操作的副本集合至少产生一个交集，即 R+W>N。Swift 默认配置是 N=3，W=2>N/2，R=1 或 2，即每个对象会存在 3 个副本，这些副本会尽量被存储在不同区域的节点上；W=2 表示至少需要更新 2 个副本才算写成功；当 R=1 时意味着某一个读操作成功便立刻返回，此种情况下可能会读取到旧版本（弱一致性模型）；当 R=2 时，需要通过在读操作请求头中增加 x-newest=true 参数来同时读取 2 个副本的元数据信息，然后比较时间戳来确定哪个是最新版本（强一致性模型）；如果数据出现了不一致，后台服务进程会在一定时间窗口内通过检测和复制协议来完成数据同步，从而保证达到最终一致性。如图 所示：



![86123326566321700](https://gitee.com/hanstack/hanstack_image/raw/master/image/86123326566321700.png)

#### Ring的数据结构

环是为了将虚拟节点（分区）映射到一组物理存储设备上，并提供一定的冗余度而设计的，其数据结构由以下信息组成：



- 存储设备列表、设备信息包括唯一标识号（id）、区域号（zone）、权重（weight）、IP 地址（ip）、端口（port）、设备名称（device）、元数据（meta）。
- 分区到设备映射关系（replica2part2dev_id 数组)
- 计算分区号的位移(part_shift 整数，即图 1 中的 m)
- ![365276421683472295](https://gitee.com/hanstack/hanstack_image/raw/master/image/365276421683472295.png)

以查找一个对象的计算过程为例：

使用对象的层次结构 account/container/object 作为键，使用 MD5 散列算法得到一个散列值，对该散列值的前 4 个字节进行右移操作得到分区索引号，移动位数由上面的 part_shift 设置指定；按照分区索引号在分区到设备映射表（replica2part2dev_id）里查找该对象所在分区的对应的所有设备编号，这些设备会被尽量选择部署在不同区域（Zone）内，区域只是个抽象概念，它可以是某台机器，某个机架，甚至某个建筑内的机群，以提供最高级别的冗余性，建议至少部署 5 个区域；权重参数是个相对值，可以来根据磁盘的大小来调节，权重越大表示可分配的空间越多，可部署更多的分区。

Swift 为账户，容器和对象分别定义了的环，查找账户和容器的是同样的过程。

### Swift组件

![792003611753423029](https://gitee.com/hanstack/hanstack_image/raw/master/image/792003611753423029.png)

#### Swift API

Swift通过RESTful API的形式为自己提供自己的API，Proxy Server 承担了类似Nova-API的功能，负责接收来自用户的HTTP请求。

Swift API提供了包括存储对象，压缩对象，删除对象在内的一系列操作

#### Swift的认证组件

Swift中支持内部和外部两种类型的认证系统。一般来说外部的认证请求时只想KeyStone服务进行认证的，内部的则是由Swift的WSGI中间件Tempauth来进行认证用户。无论采用什么方式的认证系统，用户都要提交自己的credentials（证书是）给到认证系统，认证系统会返回给用户一个文本形式的token，token具有一定的时效性，token的认证结果会被缓存，用户可以在token未过期的时间内通过请求中指定token来访问Swift服务。 





----参考内容：

https://wiki.openstack.org/wiki/Swift

https://www.swiftstack.com/docs/introduction/openstack_swift.html

https://www.ibm.com/developerworks/cn/cloud/library/1310_zhanghua_openstackswift/

