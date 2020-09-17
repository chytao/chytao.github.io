---
title: "OpenStack  分享篇12 Ceilometer"
date: 2020-08-29T09:50:39+08:00
draft: true
---

### 1.Ceilometer的体系结构

Ceilometer原意为测云仪，用来测量云层的密度厚度以及高度等，人们使用这些数据来判断天气等。在云计算，使用这个概念映射IaaS层中的计量功能。

OpenStack中Ceilometer能把IaaS层内部发生的事情和系统的运行情况收集起来，产生计量数据。FS OpenStack中Ceilometer主要为上层运维组件提供监控和告警数据，共有云下可以用于计费。

下图为使用Ceilometer提供的数据对VM的一系列指标进行监控，包括CPU使用率、内存使用率、网络流入速率、磁盘IO等。

![image-20200829100428733](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829100428计量.png)

下图为使用Ceilometer上报的数据提供告警消息。

![image-20200829100838184](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-202008291008告警.png)

Ceilometer整体采用了高度可扩展性的设计思想，非常利于开发者开发插件扩展或者定制其功能。

![image-20200829103506803](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-202008291035架构3.png)

Ceilometer采用以下三种方式获取测量数据：

- 第三方的数据发送者把数据以通知消息（Notification Message）的形式发送到消息总线（Notification Bus）上，Notification Agent会获取这些通知事件，从中提取测量数据。
- Polling Agent会根据配置，定期主动通过各种API或者其他通信协议去远端或者本地不同的服务实体中获取所需要的测量的数据。
- 用户可以通过调用RESTful API直接把利用其他方式获取的任意测量数据发送给Ceilometer

以上Ceilometer通过三种方法获取到测量数据后，会将数据转化为符合某种标准格式的数据采样（Sample）通过内部总线发送给Notification agent。然后Notification Agent根据用户定义的Pipeline来对采样进行转换（Transform）和发布（Publish）。如果根据Pipeline的定义，这个数据采样（Sample）最后被发布给Collector的话，Collector会把这个数据采样保存在数据库中。

​	用户可以通过Ceilometer API对保存在数据库中的测量数据进行条件查询、数据聚合（Data Aggregate）等操作。

### 2.Pipeline

在Ceilometer汇总，适用于不同应用场景下的测量数据，采样频率可能有不同的要求。例如对于计费的数据，采样的频率比较低，典型值为5~30min；对于用来监控的数据，这采样的pinljiuhui就会高很多，一般会达到1~10S。

​	另外，对于测量数据的发布方式，不同的应用场景也会提出不同的要求。对于计费的数据，就要求采用值不能丢失，并且要保证数据的不可否认性（non-repudiation）；而对于监控的数据，这方面就没有这么严格的要求。

​	在Ceilometer中引入的Pipeline的概念来解决采样频率和发布方式的问题。Pipeline的引入使得管理员可以很容易的定义某一个测量数据的采样频率和发布方式。

​	Ceilometer中，同时允许多个Pipeline，每个Pipeline都是由源（Source）和目标（sink）组成的。源中定义了需要测量哪些数据，数据的采样频率，在哪些endpoint上进行数据采样，以及这些数据的目标（sink）。目标中定义了获得的数据要经过哪些Transformer进行数据转换，并最终交由哪些Publisher进行数据发布。

![image-20200829105433727](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829105数据发布流程.png)

转换器（Transformer）可以针对一个或者多个同一种类的数据采样值进行各种不同的操作，例如改变单位、聚合计算等，最终转换为一个或者多个其他不同种类的测量数据。例如将多个CPU使用时间的测量数据采样经过Transformer进行数据转化后得出CPU的使用频率。

​	通过转换器（Transformer）进行数据转换后的数据，最终会交由Pipeline定义中的Publisher进行数据发布。Pipeline中定义了3个不同的Publisher，分别采用message Bus上的notification、kafka消息和udp数据包3中不同的方式同时将一个数据采样值发布给不同的数据接收者。

![image-20200829110031840](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-202008291100Publisher.png)

目前已知多种Publisher，具体如下：

- notifier：向通知总线上发送info级别的通知消息，采样数据（Sample）内容作为通知消息的数据载荷（payload）。
- kafka：向kafka消息队列发送
- direct：直接调用collector dispatcher保存到数据后台，绕开collector
- udp：将采样数据封装在udp包内，然后向某个管理员可配置的udp地址和端口进行发送。
- file：把采样数据内容以log形式保存在某个管理员可配置的http endpoint
- http：把采样的数据以http协议发送给某一个http endpoint。

### 3.Polling Agen 与Pollster插件

​	polling agent的作用是根据Pipeline的定义，定期调用不同的Pollster插件去轮询获得Pipeline中定义的测量值，再根据Pipeline的定义，对这些采用值进行转换和发布。

​	Pollster插件是符合特定接口标准的Python类，Agent会调用这些Pollster插件来获得不同的测量数据。Pollster插件可以自行开发来获取新的测量值。

目前Ceilometer中有三种不同类型的Polling Agnet。

- Commpute Agent需要被部署在运行nova-compute服务的计算节点上，主要是用来和Hypervisor进行通信的，轮询获取Hypervisor相关的测量值。
- Central Agent可以被部署在任意节点上，他用来和远程的各种不同的实体和服务进行通信，获取不同的测量值。
- 在J版本中，添加的IPMI Agnet需要部署在支持IPMI的节点上，用来获取本机IPMI的相关测量值。

### 4.Notification Agent与Notification Listeners插件

​	Ceilometer项目中获取数据的方式除了用Polling Agent轮询之外还可以通过监听Notification Bus上的通知消息（Notification Message） 消息来实现。这是由Notification Agent通过Notification Listener来实现的。



### 5.Collector与Dispatcher插件

当Ceilometer获得的测量数据通过Pipeline发布（Publisher）出去后，需要有一个数据接收者获得这些数据并保存起来，以便对数据进行后续的进一步的处理。Collector的作用就是把接收到数据保存在数据后台中。

![image-20200829113946768](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-202008291139Collector架构.png)

对于每个Collector，管理员可以为其配置一个或者多个Dispatcher。对于每一个所收到的采样数据，Collector会调用所有的Dispatcher，这些Dispatcher来决定如何处理数据。目前已有4个Dispatcher。

- database dispatcher:把采样数据或者Event事件保存在后台数据库中。
- file dispatcher：把采样数据或者Event事件以log的形式保存在文件中
- gnocchi dispatcher：把采样数据或者Event事件保存在Gnocchi中
- http dispatcher：把采样数据或者Event事件以http封装发送给某个http endpoint。

### 6.Storage/DB

为了支持多种不同的后台数据库，Ceilometer引入了数据库抽象层，通过数据库抽象层，上层可以通过统一的接口向后台不同的数据库写入要保存的数据，也可以通过统一的接口从后台不同的数据库中获得一致的格式所表示的数据。

目前Ceilometer支持4中不同的数据库后台：MongoDB,MySQL，PostgreSQL和Hbase。需要注意的是不同的数据库后台，所支持的功能性和性能有所不同，也可以根据自己的需要进行选择。

​	和OpenStack中其他的项目比起来，Ceilometer所保存的数据量会大很多。所以虽然Ceilometer的默认后台数据库是MySQL，但是在历史上一般建议在实际的生产环境中采用MongoDB为后台数据库。MongoDB作为关系型数据库有着更多更好的特性。

注：Telemetry社区目前建议使用Gnocchi代替上面所说的数据库来保存采样数据。