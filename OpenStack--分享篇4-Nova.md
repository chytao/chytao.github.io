---
title: "OpenStack分享篇4 Nova"
date: 2020-05-07T17:40:27+08:00
draft: true
---

Nova由多个提供不同功能的独立组件构成，外部通过 REST API进行通信，对内使用RPC进行通信，使用一个中心DB来存储数据。每个组件都可以部署一个或者多个来实现横向扩展。这样的架构是被大部分OpenStack项目所采用的。



在详细的介绍Nova组件之前我们先来了解一下与Nova相关的REST API  RPC

### REST API

https://www.mulesoft.com/resources/api/what-is-an-api

#### 什么是API 

API 是Application Programming Interface（应用程序接口）的缩写。基本上API指定了软件组件之间应该如何进行交互。此外在图形用户界面（GUI）组件上进行编程时会使用API，好的APi可以通过提供所有构件来简化程序的开发。

#### API示例

当您在使用应用程序是，该应用程序会链接到Internet网络上并将数据发送到服务器，然后服务器检索数据，对其进行解释，执行必要的操作，然后返回到应用，由应用对返回的数据进行解释，并以刻度的方式为用户提供所需的信息。

举个现实中的例子，当您使用手机上的地图应用查询交通信息时，由地图应用将您所查询的信息通过Internet发送到服务器，再由InterNet上的服务器对对您查询的交通信息进行检索和处理，返回到您的应用上。您的地图应用对这些返回的信息进行处理组织成用户可读的形式，简单来说就是讲计算机语言组织成人类语言的形式进行展示。此时您就可以看到您查询的交通信息的结果了。

#### 现代API

现代API遵循的标准（通常是HTTP和REST），这些标准对开发人员友好，易于访问且广泛理解。



### REST

https://www.sitepoint.com/developers-rest-api/

REST是 ***Representational State Transfer\*** 的缩写；它是由罗伊~菲尔丁（Roy Fielding）提出的，用来描述创建HTTP API的标准方法的，他发现四种常用的行为，view 查看create创建 delete删除 edit便编辑都可以直接映射到HTTP 中已实现的GET,POST,PUT和DELETE方法。大多数情况下，当你在使用你的浏览器的点点看看的时候，其实只用到HTTP的GET方法。GET方法是在你向因特网请求资源的时候才会用到的。当你提交一个表单时，你就会经常用到POST方法来回传数据到网站上。至于其他的几种方法，某些浏览器可能根本就没有去完全实现它们。但是，如果是供我们使用的话，就没什么问题。问题是我们有很多要选择去帮助描述这四大行为的HTTP方法，我们将会用到那些已经知道如何去使用这些不同的HTTP方法的客户端类库。

REST是两个计算系统之间通过HTTP进行通信的一种方式；类似于WEB浏览器和服务器。浏览器使用的REST可以被视为互联网的语言，随着云计算的使用不断增加，云计算消费者正在使用公有API和组织对Web服务进行访问，REST是构建API的合理选择，改API允许用户在分布式环境中灵活的链接到云服务，并与之建立管理和交互。RESTful API被Amazon，Google，Twitter等网站使用。

#### REST API如何工作

RESTful API分解事物以创建一系列小模块。每个模块都处理事务的特定基础部分。这种模块化为开发人员提供了很大的灵活性，但是对于开发人员来说，从头开始设计REST API可能会面临挑战OpenStack，Amazon等提供了开发人员使用的模型。

### MQ（Message Queue ）：消息队列

https://aws.amazon.com/cn/message-queue/

#### 消息队列基本知识

在现代云架构中，应用程序被分解为多个规模较小且更易于开发、部署和维护的独立构建块。消息队列可为这些分布式应用程序提供通信和协调。消息队列可以显著简化分离应用程序的编码，同时提高性能、可靠性和可扩展性。

借助消息队列，系统的不同部分可相互通信并异步执行处理操作。消息队列提供一个临时存储消息的轻量级缓冲区，以及允许软件组件连接到队列以发送和接收消息的终端节点。这些消息通常较小，可以是请求、恢复、错误消息或明文信息等。要发送消息时，一个名为“创建器”的组件会将消息添加到队列。消息将存储在队列中，直至名为“处理器”的另一组件检索该消息并执行相关操作。

![74672075901723209](https://gitee.com/hanstack/hanstack_image/raw/master/image/74672075901723209.png)

#### 为什么要用消息队列

1.能够通过异步处理提高系统性能（削峰填谷，减少相应所需的时间）

​	在不适用消息队列的情况系，客户端发起的数据请求直接写入数据库，在高并发的情况下回使得数据库压力剧增，是得相应速度变慢。引入消息队列之后，用户请求的数据发送给消息队列后立即返回，再由消息队列的消费者进程从消息队列中获取数据，异步写入数据库。由于消息队列服务器的处理速度快于数据库，因此能够提升性能。

2.降低系统耦合性

![202005072350](https://gitee.com/hanstack/hanstack_image/raw/master/image/202005072350.png)

消息队列使用发布-订阅模式工作，消息发送者（生产者）发布消息，一个或者多个消息接受者（消费者）订阅消息。

在上图所示的逻辑图中，生产者和消费者之间不是耦合关系，消息队列MQ在其中充当中间件的作用。消息的发送者发送消息到分布式消息队列便结束对消息的处理，消息接受者从分布式消息队列中获取数据后进行后续的处理动作，而并需要关心消息的来源。对于新增的业务如果对该类消息感兴趣，即可以通过订阅的方式订阅该消息，对原有的系统和业务并不产生影响。从而实现良好的可扩展性和分布式。

消息队列不仅有发布-订阅模式，还有点对点订阅模式（一个消息只有一个消费者）。

#### 集中消息队列

--参考java工程师面试突击第一季

ActiveMQ 的社区算是比较成熟，但是较目前来说，ActiveMQ 的性能比较差，而且版本迭代很慢，不推荐使用。

RabbitMQ 在吞吐量方面虽然稍逊于 Kafka 和 RocketMQ ，但是由于它基于 erlang 开发，所以并发能力很强，性能极其好，延时很低，达到微秒级。但是也因为 RabbitMQ 基于 erlang 开发，所以国内很少有公司有实力做erlang源码级别的研究和定制。如果业务场景对并发量要求不是太高（十万级、百万级），那这四种消息队列中，RabbitMQ 一定是你的首选。如果是大数据领域的实时计算、日志采集等场景，用 Kafka 是业内标准的，绝对没问题，社区活跃度很高，绝对不会黄，何况几乎是全世界这个领域的事实性规范。

RocketMQ 阿里出品，Java 系开源项目，源代码我们可以直接阅读，然后可以定制自己公司的MQ，并且 RocketMQ 有阿里巴巴的实际业务场景的实战考验。RocketMQ 社区活跃度相对较为一般，不过也还可以，文档相对来说简单一些，然后接口这块不是按照标准 JMS 规范走的有些系统要迁移需要修改大量代码。还有就是阿里出台的技术，你得做好这个技术万一被抛弃，社区黄掉的风险，那如果你们公司有技术实力我觉得用RocketMQ 挺好的

kafka 的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms 级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展。同时 kafka 最好是支撑较少的 topic 数量即可，保证其超高吞吐量。kafka 唯一的一点劣势是有可能消息重复消费，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略这个特性天然适合大数据实时计算以及日志收集。

### Nova架构

目前的Nova主要由Nova-API，Nova-Conductor，Nova-COmpute，Nova-Scheduler四个核心组件所组成，组件之间采用RPC进行通信。

![architecture](https://gitee.com/hanstack/hanstack_image/raw/master/image/architecture.svg)

### Nova—API

Nova-API是进入Nova的HTTP接口，可以通过部署多个来实现横向扩展。Nova-APi作为客户端和Nova 之间的中间层，Nova-API扮演了一个桥梁，或者说中间人的角色。Nova-API把客户端的请求传达给Nova，待到Nova处理完成之后传递给客户端。

### Nova-Conductor

nova 的版本：https://docs.openstack.org/train/#

![202005071805](https://gitee.com/hanstack/hanstack_image/raw/master/image/202005071805.png)

Nova-conductor服务最初在Gizzly版本中发布，作为Nova项目的

核心模块之一，他在整个Nova中相当于组织者的角色，主要提供了3项基本功。

#### Nova-Conductor的长时任务编排功能

Nova-Conductor连接了Nova-API；Nova-compute，Nova-scheduler服务，所以它首先提供了长时任务编排（task orchestration）功能。Nova将所有耗时长，跨节点，易错但相对固定的处理流程抽象成任务，包括虚拟机启动，热迁移（Vmotion），冷迁移等。Nova-Conductor作为任务的组织者，不仅能同时进行的多个任务的状态进行跟踪，还能完成错误处理，恢复等一系列的功能。

#### Nova-Conductor的数据库代理访问机制

Nova-Conductor为Nova-Compute提供了数据代理访问机制，不仅是数据库访问的一层安全保障，还在数据库升级过程中未旧的Nova-Compute节点提供了向下的兼容性。在模块之间的通信过程中，由于不同的服务可能运行在不同的代码版本上，Nova-Conductor为传输数据的对象提供了版本兼容的功能，让处在不同版本的Nova服务能够识别RPC请求中数据的对象版本，并且完成版本的自动转换。

#### Nova-Conductor实现高可用性和性能的横向扩展

Nova-Conductor服务本身是无状态的（ 对服务器程序来说，有两个基本假设十分重要，究竟服务器是基于状态请求还是无状态请求。状态化的判断是指两个来自相同发起者的请求在服务器端是否具备上下文关系。如果是状态化请求，那么服务器端一般都要保存请求的相关信息，每个请求可以默认地使用以前的请求信息。而无状态请求则不行，服务器端所能够处理的过程，他的处理信息必须全部来自于请求所携带的信息以及其他服务器端自身所保存的、并且可以被所有请求所使用的公共信息。        无状态的服务器程序，最著名的就是WEB服务器。每次HTTP请求和以前都没有啥关系，只是获取目标URI。得到目标内容之后，这次连接就被杀死，没有任何痕迹。在后来的发展进程中，逐渐在无状态化的过程中，加入状态化的信息，比如COOKIE。服务端在响应客户端的请求的时候，会向客户端推送一个COOKIE，这个COOKIE记录服务端上面的一些信息。客户端在后续的请求中，可以携带这个COOKIE，服务端可以根据这个COOKIE判断这个请求的上下文关系。COOKIE的存在，是无状态化向状态化的一个过渡手段，他通过外部扩展手段，COOKIE来维护上下文关系。），用户可以在运行过程中任意调节Nova-conductor的数量和位置，在性能和稳定性要求高的部署环境中，用户可以非常容易的对Nova-conductor服务实现高可用和性能的横向扩展。Nova-Conductor的横向扩展能力带来的另一个好处就是，Nova-compute通过Nova-Conductor对数据库访问的性能也有了相应的保障。在没有Nova-Conductor时Nova-Compute和数据库之间的交互采用协程，Nova-Compute在访问数据库时都是阻塞的。在引入了Nova-Conductor之后能够通过Nova-compute创建多个协程通过nova-Conductor使用非阻塞的RPC协议来王文数据库。当然RPC也有他的一些确定如RPC是有延时的。



### Nova-Scheduler

https://docs.openstack.org/nova/latest/user/filter-scheduler.html

选择一个虚拟机在哪个主机上运行调度的方式有很多，目前Nova中实现的调度器有以下几个。

FileterScheduler：过滤调度器；默认载入的调度器，根据指定的过滤条件和权重挑选最佳节点。

CachingScheduler：与FileterScheduler功能相同，在其基础上将主机资源信息缓存在本地内存中国，然后通过后台的定时任务定时从数据库中获取最新的主机资源信息。

ChanceScheduler：随机调度器；从所有的Nova-compute服务正常运行的节点随机选择。

FackScheduler：伪调度器；用于单元测试，没有任何实际功能的调度器



#### Nova-Scheduler实现资源调度的过程

#### 

#### 1.调度器缓存更新

Nova-Scheduler在进行调度决策前，需要从数据库中获取到各个主机的资源数据，这些数据的收集与存储都由Nova-compute进行负责。nova-Compute是将这些数据以即使的方式更新到数据库中，并通过周期性的任务保证资源数据的准确性。由于Nova-Scheduler无法更新数据库，所以在选择最佳主机时要在内存中保存先前决策的情况，这是用过在调度器内存中国单独维护了一份缓存实现的，缓存中包含了最近一次读取数据库的情况以及最近调度器决策导致资源变华。为了维护该缓存的准确性，nova-scheduler在作为一个虚拟机启动请求作出决策之后，他都要将其更新，并从主机的可用资源中去掉VM使用的资源。

#### 2.Filtering

Filtering是使用配置文件指定的各种Filters去过滤掉不符合条件的主机。在这个阶段根据各个主机当前可用资源情况过滤掉那些不能满足虚拟机要求的主机。

在Ocata版本的OpenStack Nova 项目中的Filtering公有27个Filter能够处理各种信息。

![439917909026612786](https://gitee.com/hanstack/hanstack_image/raw/master/image/439917909026612786.png)

#### 分类：

| 主机可用资源             | 内存，磁盘，CPU，PCI设备和NUMA拓扑                           |
| ------------------------ | ------------------------------------------------------------ |
| 主机类型                 | 虚拟机类型和版本，CPU类型和指令集类型                        |
| 主机状态                 | 主机是否处于活动状态，CPU的使用率，虚拟机启动数量，繁忙程度，是否可信 |
| 主机分组情况             | Avaible Zone，Host Aggergaters信息                           |
| 启动请求参数             | 请求虚拟机类型（Flavor），镜像信息（image）,请求重试次数，启动提示信息（hint）等 |
| 虚拟机的亲和性和反亲和性 | 与其他虚拟机是否在同一台主机上                               |
| 元数据处理               | 主机元数据，镜像元数据，虚拟机元数据，主机聚合（host Aggergates）元数据 |

#### Filter列表：

| **AllHostsFilter**                    | 不过滤。它传递所有可用的主机。                               |
| ------------------------------------- | ------------------------------------------------------------ |
| **ImagePropertiesFilter**             | 根据实例镜像上定义的属性过滤虚拟机。                         |
| **AvailabilityZoneFilter**            | 按可用性区域过滤主机。它传递与实例属性中指定的可用区域匹配的主机。使用逗号指定多个区域。然后，过滤器将确保其与指定的任何区域匹配 |
| **ComputeCapabilitiesFilter**         | 检查主机计算服务提供的功能是否满足与实例类型相关的任何额外规范。它传递可以创建指定实例类型的主机。 |
| **AggregateInstanceExtraSpecsFilter** | 检查汇总元数据是否满足与实例类型相关联的任何额外规范（没有范围或以范围aggregate_instance_extra_specs）。它传递可以创建指定实例类型的主机。额外的规格可以具有与相同的运算符 ComputeCapabilitiesFilter。要为同一键指定多个值，请使用逗号。例如，“ value1，value2”。如果未指定extra_specs，则将传递所有主机。 |
| ComputeFilt                           | 挑选出所有处于活跃（Active）状态的主机。                     |
| `AggregateCoreFilter`-已淘汰；        | 使用每个聚合cpu_allocation_ratio设置按CPU内核号过滤主机 。如果未找到每个汇总值，则它将回落到全局默认值 cpu_allocation_ratio。如果为主机找到一个以上的值（表示该主机位于两个不同的聚合中，且比率设置不同），则将使用最小值。 |
| **IsolatedHostsFilter**               | 过滤器基础上 [`filter_scheduler.isolated_images`](https://docs.openstack.org/nova/latest/configuration/config.html#filter_scheduler.isolated_images)， [`filter_scheduler.isolated_hosts`](https://docs.openstack.org/nova/latest/configuration/config.html#filter_scheduler.isolated_hosts) 和[`filter_scheduler.restrict_isolated_hosts_to_isolated_images`](https://docs.openstack.org/nova/latest/configuration/config.html#filter_scheduler.restrict_isolated_hosts_to_isolated_images) 标志。 |
| JsonFilter                            | 允许使用简单的基于JSON的语法来选择主机                       |
| `AggregateRamFilter`-已淘汰           | 通过按聚合[`ram_allocation_ratio`](https://docs.openstack.org/nova/latest/configuration/config.html#DEFAULT.ram_allocation_ratio)设置按RAM过滤主机 。如果未找到每个汇总值，则它将回落到全局默认值 [`ram_allocation_ratio`](https://docs.openstack.org/nova/latest/configuration/config.html#DEFAULT.ram_allocation_ratio)。如果为主机找到一个以上的值（表示该主机位于两个不同的聚合中，且比率设置不同），则将使用最小值。 |
| `AggregateDiskFilter`-已淘汰          | 使用每个聚合disk_allocation_ratio设置按磁盘分配过滤主机 。如果未找到每个汇总值，则它将回落到全局默认值 disk_allocation_ratio。如果为主机找到一个以上的值（表示该主机处于两个或多个具有不同比率设置的不同聚合中），则将使用最小值。 |
| **NumInstancesFilter**                | 按实例数过滤计算节点。实例过多的节点将被过滤。如果主机filter_scheduler.max_instances_per_host上已不存在该主机，则调度程序将忽略 该主机。 |
| **AggregateNumInstancesFilter**       | 按每个聚合filter_scheduler.max_instances_per_host 设置按实例数过滤主机。如果未找到每个汇总值，则它将回落到全局默认值filter_scheduler.max_instances_per_host。如果为主机找到一个以上的值（表示该主机处于两个或多个不同的聚合中，每个主机设置具有不同的最大实例），则将使用最小值。 |
| **IoOpsFilter**                       | 通过并发I / O操作过滤主机。并行I / O操作过多的主机将被过滤。 [`filter_scheduler.max_io_ops_per_host`](https://docs.openstack.org/nova/latest/configuration/config.html#filter_scheduler.max_io_ops_per_host)设置。允许在此主机上运行的I / O密集型实例的最大数量，如果[`filter_scheduler.max_io_ops_per_host`](https://docs.openstack.org/nova/latest/configuration/config.html#filter_scheduler.max_io_ops_per_host) 在其上运行的实例超过了诸如构建/调整大小/快照等实例，则调度程序将忽略该主机 。 |
| **AggregateIoOpsFilter**              | 通过每个聚合filter_scheduler.max_io_ops_per_host设置按I / O操作过滤主机 。如果未找到每个聚合的值，则它将回落到全局默认值 oslo.config：option：filter_scheduler.max_io_ops_per_host。如果为主机找到一个以上的值（表示该主机处于两个或多个不同的聚合中，且具有不同的最大io操作设置），则将使用最小值。 |
| `PciPassthroughFilter` -筛选器        | 用于在主机上具有可满足风味'extra_specs'中的设备请求的设备的情况下，在主机上调度实例。 |
| SimpleCIDRAffinityFilter              | 允许在同一IP块内的主机上创建新实例。                         |
| DifferentHostFilter                   | 允许实例与一组实例位于不同的主机上。                         |
| SameHostFilter                        | -将实例与一组实例中的另一个实例放在同一主机上。              |
| `RetryFilter`-已淘汰                  | 筛选已尝试进行调度的主机。仅通过以前未尝试过的主机。         |
| **AggregateTypeAffinityFilter**       | 通过聚合限制instance_type。<br/>如果未设置instance_type密钥或instance_type聚合元数据值包含所请求的instance_type的名称，则此筛选器将通过主机。instance_type元数据条目的值是一个字符串，可以包含单个instance_type名称或逗号分隔的instance_type名称列表。例如“ m1.nano”或“ m1.nano，m1.small” |
| **ServerGroupAntiAffinityFilter**     | 此过滤器为服务器组实现了反关联性。首先，您必须通过服务器组API创建一个策略为“反亲和力”的服务器组。然后，当您引导新服务器时，提供调度程序提示“ group = <uuid>”，其中<uuid>是您创建的服务器组的UUID。这将导致服务器被添加到组中。调度服务器后，将在该组中的所有服务器之间强制执行反关联性。 |
| **ServerGroupAffinityFilter**         | 此过滤器的工作方式与ServerGroupAntiAffinityFilter相同。区别在于，创建服务器组时，应指定“亲和力”策略。 |
| **AggregateMultiTenancyIsolation**    | 隔离特定集合体中的租户。要指定多个租户，请使用逗号。例如。“ tenant1，tenant2” |
| **AggregateImagePropertiesIsolation** | 根据图像属性和聚合元数据隔离主机。使用逗号为同一属性指定多个值。然后，过滤器将确保至少一个值匹配。 |
| **MetricsFilter**                     | 根据指标weight_setting过滤主机。仅传递具有可用指标的主机     |
| NUMATopologyFilter                    | 根据实例请求的NUMA拓扑过滤主机（如果有）。                   |

#### 3.Weighting权重

Weighting是指对所有复合条件的主句进行权重的计算并排序，从而选出最佳的一个主机。经过各种过滤器过滤之后，会得到一个最终的主机列表，保存了所有通过指定过滤器的主机。由于列表中国可能存在多个主机，所以调度器还需要在他们之中选择最优的一个，类似于Filtering，这个过程需要调用指定的各种weigher模块，得出每个主机总的权重值。

![488849016257043738](https://gitee.com/hanstack/hanstack_image/raw/master/image/488849016257043738.png)

#### 权重模块列表

| **ServerGroupSoftAffinityWeigher** | 基于在同一服务器组上运行的实例数来计算权重。 |
| ---------------------------------- | -------------------------------------------- |
| **BuildFailureWeigher**            | 最近失败的引导尝试次数加权主机               |
| **CrossCellWeigher**               | 根据主机所在的单元对主机进行权重计算         |
| RAMWeight                          | 根据主机的内存来计算权重                     |



### Nova-Compute

https://docs.openstack.org/nova/latest/

nova-compute负责虚拟机生命周期的管理包括instance的launch，shutdown，reboot，suspend，respernd，resume，terminate，resize，migration，snapshot等。

nova-compute位于在hypervison层。Nova-Compute负责在数据中更新主机的资源使用情况，一般来说一个物理主机上有一个nova-compoute进程，由其负责该进程所在主机上的资源管理，并通过nova-conductor向DB数据库中报告。

Nova-Compute会通过两种途径来更新当前主机所对应的数据库记录。一是Resource Tracker的Claim机制，二是使用周期性任务Periodic Task.

#### Claim机制

当一台主机被Nova-Scheduler的多个决策同时选中并发送创建虚拟机的请求时，这台被选中的虚拟机可能并没有足够的资源去满足这多个决策的虚拟机创建请求。Clim机制便是在创建之前先对主机的可用资源进行测试，查看能否满足需求，如果满足通过nova-conductor更新数据库，将虚拟机创建所申请的资源从主机的可用资源中减去，如果创建失败，会通过Clim恢复之前减掉的资源。

#### Period Task

存在一个周期性任务用于更新主机的资源数据。他每次通过hypervison重新获取主机的资源数据，并将这些信息更新到数据库中。



claim和Period这两种更新主机数据资源的方式并不冲突。Clim是在每次主机发生消耗时进行更新，保证了数据库中可用资源数据的时效性。能够为nova-schduler提供最新的数据。周期性的period更新是为了保证数据库内数据的准确性，他每次通过hypervison重新获取主机的资源数据，并将这些信息更新到数据库中。



### NOVA创建虚拟机的过程

![202005071804](https://gitee.com/hanstack/hanstack_image/raw/master/image/202005071804.png)

1.Nova-API通过北向接口接收创建虚拟机的请求（RESTful 请求）

2.Nova-API像keyStone进行认证授权，授权成功以后，Nova-API通过RPC向nova-Conductor发送信息

3.Nova-conductor生成详细的虚拟机信息，Nova-Scheduler依据这些信息为虚拟机选择一个最佳主机（Fileting，weighting）

3.Nova-Conductor再通过RPC调用Nova-Compute创建虚拟机

4.Nova-Compute首先使用Resource Tracker中的Clim机制检测主机的可用资源能否满足创建虚拟机的需求，然后通过具体的Virt Driver创建虚拟机。











--本文由女神提供部分材料的整理编排