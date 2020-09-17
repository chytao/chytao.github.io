---
title: "OpenStack  分享篇6 Glance"
date: 2020-05-11T01:23:02+08:00
draft: true
---

### Glance的功能

在OpenStack中Glance提供虚拟机的镜像（Image）服务。值得注意的是，Glance本身并不参与大量数据的存储，Glance镜像的存储需要依赖于Swift等项目来完成。

### Architecture of Glance service in OpenStack

--https://docs.openstack.org/glance/pike/contributor/architecture.html



![architecture-of-glacne](https://gitee.com/hanstack/hanstack_image/raw/master/image/architecture-of-glacne.png)

Glance本身并不负责实际的存储，只是用来完成一些镜像的管理工作，所以Glance的功能比较单一，所包含的相应的组件也相对较少。

Glance主要由Glance-API与Glance-registry两个服务组成。Glance-API是进入Glance的入口，负责接收RESTful请求，然后后台的Swift，Amazon S3等存储系统完成镜像的存储与获取。

Glance的Store模块在Juno版本之前是Glance的一个子组件，在Juno之后Glance_Store成为一个独立的组件从Glance中独立出来，但是截至到小韩（笔者）书写这篇博客之前，Glance_Store依然只是用于Glance和Glare项目的特定用途。（https://docs.openstack.org/glance_store/latest/index.html）

### Glace组件的介绍

#### Glance-API

Glance-API主要提供镜像管理的功能，比如Image的导入和导出，镜像元数据的管理等。目前Glance-API使用的v2版本，在v2版本的Glance-API在一定程度上整合了glance-registry的服务功能。v1版本的Glance-API已经在Newton版本中弃用。

Glance-API能够为镜像建立本地缓存，实现API服务节点数量的扩展，提供多个API节点为一个Image提供服务的模式以提高效率。如果只有一个API节点则缓存服务没有意义。本地的Image Cache是对Image文件的完整复制，但是对用户来说这是完全透明的，用户无需关心Image的具体存放位置，无论Image是存放在后端存储还是本地存储用户都是无感知的。

Glance-API接收由GlanceClite发送的RESTful请求；Glance-API会导入Glance_Store项目并初始化后台的存储系统。

#### Image

Image是Glance所管理的主要资源，类似于VM的虚拟机模板（template）,它预装了OS。从Image启动VM，VM被删除后Image不受影响依然存在。Image只是作为VM的启动模板，VM实例上的改动并不会体现在Image上。

镜像的元数据负责精确完整的描述镜像的信息；

id：Image唯一的UUID

Name：image 名字

owner:所有者

size：字节表示的Image大小

等---

#### Glance-registry

Glance-registry负责处理与镜像元数据相关的请求，当Glance-API接收到来自GlanceClite的RESTful消息包含有关镜像元数据的请求便将有关镜像元数据的请求消息发送给Glance-registry。然后有Glance-registry负责解析请求的内容，并与DB数据库进行交互，存取或者更新镜像的元数据（镜像元数据指的是保存在数据库中有关Image的一些消息）

#### DB

只负责存储镜像的元数据



### Glance创建Image

Glace-API接收到GlanceClite的RESTful请求，通过WSGI Route模块路由到具体的操作，一般会包含两个操作，一个是负责加载Glance-Store并且初始化后端存储完成对镜像的存取操作；一个是针对Glance DB中的元数据执行操作







