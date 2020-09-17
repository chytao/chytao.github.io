---
title: "OpenStack  分享篇8 Keystone"
date: 2020-05-13T02:09:08+08:00
draft: true
---

https://www.openstack.org/software/project-navigator/openstack-components#openstack-services

## 什么是keyStone

keyStone是OpenStack中负责管理身份服务（Identity Service），即KeyStone，是OpenStack早期版本就独立出来的一个核心项目，在OpenStack的整体框架中，KeyStone的作用类似于一个服务总线，将Nova，Cinder，Horzion，Swift，Glance以及Neutron等其他服务逐渐都通过KeyStone来注册其服务的Endpoint（访问服务的url），这些服务由KeyStone体精身份认证之后，才能获得该服务的Endpoint来进行访问。

## KeyStone Architecture

KeyStone的中的一些基本概念

https://docs.openstack.org/keystone/latest/getting-started/architecture.html

Service：服务；比如Nova；Cinder，Glance；Swift等，根据User，Tenant和Role一个服务可以确认当前用户名是否具有访问其资源的权限。服务对外暴露一个Endpoint，用户只有通过这些Endpoint才能对这些服务进行访问。使用身份服务验证user/project.创建一个token在成功后返回这个token。

Identity：身份服务；提供身份验证凭证以及相关的user/group的数据信息。一般情况下；此数据由Identity Service进行管理，在复杂场景下数据由Endpoint Service进行管理。例如在keyStone作为LDAP（**轻型目录访问协议**（[英文](https://baike.baidu.com/item/英文)：**Lightweight Directory Access Protocol**，[缩写](https://baike.baidu.com/item/缩写)：**LDAP**，/ˈɛldæp/）是一个开放的，中立的，工业标准的[应用协议](https://baike.baidu.com/item/应用协议)，通过[IP协议](https://baike.baidu.com/item/IP协议)提供访问控制和维护分布式信息的目录信息。）的前端时，KeyStone仅仅负责传递消息给LDAP，此时LDAP才是事实上的源头。

Users：用户；代表单个API的使用者，用户本身必须由特定的域所持有，因此所有用户名不是全局唯一的，而仅仅是其所属的域的唯一。通过KeyStone访问OpenStack服务的个人，系统或者是某个服务，KeyStone会通过认证信息（Credential，比如密码等）验证用户请求的合法性，通过验证的用户会被分配到一个特定的令牌，该令牌可以用作对后续资源访问的一个通行证。

Group：组；代表的是用户集合的容器。组本身必须由特定的域所持有，组不是全局唯一的，仅仅是其所属的域的唯一。可以向Group中添加用户，并直接向Group分配角色，那么在Group中的用户自动继承对Group添加的角色，通过引入Group的概念，能够实现对用户组的管理，能够使得KeyStone同时管理一组用户的权限。

Resource：资源；负责提供有关project和Domain的数据信息。

Projects：项目；项目是各个服务中的一些可以访问的资源的集合，例如在Nova中，我们可以将project理解为一组虚拟机的拥有者，在Swift中则是一组容器的拥有者。因此在创建虚拟机或者容器是需要指定他们所属的project，否则会将创建的虚拟机或者容器添加到默认的项目下。user总是绑定在某些project上的，用户在访问project所属的资源前，必须具有对该project的访问权限，或者说在特定的project下赋予了特定的角色。project不是全局唯一，仅在它所属的Domain中唯一即可。

Domain：域。KeyStone是一个虚拟的概念，由特定的项目（Project）来承担。一个Domain是一组user,project或者Group的容器，一个Domain对应一个大的机构，一个数据中心，所以Domain是全局唯一的。云服务的客户是Domain的拥有者，他们可以在自己的Domain上任意的创建Project，User，Group和Roles。通过引入Domain云服务的客户可以对其所拥有的多个Project进行管理，而不必针对单一的Project进行分别管理。

​	域名：在所有域中国都是全球唯一的

​	Roles：在所属域中唯一

​	user:在所属域中唯一。

​	project：在所属域中唯一

​	group：在所属域中唯一

Assigment

Roles：角色；一个用户所具有的角色，角色不同意味着权限不同。用户所拥有的角色决定了他所能访问资源的权限；能够访问哪些资源，能对哪些资源进行管理。一个用能可以被赋予一个domain或者project的角色。一个被赋予Domain的Role则被认定对这个Domain中的所有Project都具有相同的角色。同样的一个被赋予Project的橘色那么它将对这个Project具有权限。Roles能够进行继承，也就是说在一个项目树内假设Role对树根有权限，那么他便对所以的project有权限。

Role Assignment

Token：令牌。令牌是允许访问特定资源的凭证。无论通过何种方式，KeyStone最终的目的是对外提供一个访问特定资源的凭证。用户通过Credential获取在某个项目下的令牌。

Catalog：提供用于端点发现的注册表

Credentials:凭证。用户的用户名和密码。



### Domain;Project;user;Group;Role示意图

![2020-5-13-1-27](https://gitee.com/hanstack/hanstack_image/raw/master/image/2020-5-13-1-27.png)

## KeyStone的功能

基于上文对KeyStone的介绍；KeyStoen主要有Authentication（认证）；Token(令牌)；Catalog（目录）和policy（安全策略；或称为访问控制）4个核心功能。

Authentication：对用户身份进行验证，用户的身份凭证通常是以用户名和密码的方式呈现。认证服务同时提供了与该用户相关的元数据，例如用户的项目角色。

Token：确认用户的身份之后，会提供给用户一个核实该身份并用于后续资源访问请求的令牌。Token服务则验证并管理用于验证身份的令牌。KeyStone会颁发给通过认证服务的用户两种类型的令牌。

​	一类是无明确访问范围的令牌（unscoped token），此种类型的令牌存在的主要目的是用来保存用户的Credential，可以基于此令牌获取有确定访问范围的的令牌（scoped token）。

Catalog：catalog对外提供一个服务的查询目录，或者说是每个服务的可访问Endpoint列表。服务目录存在所有服务的Endpoint信息，服务之间的资源访问首先需要获取到该资源的Endpoint信息，通常是一些URl列表。服务目录存有所有服务的Endpoint信息，服务之间的资源访问首先要从服务目录上获取到该资源的Endpoint信息。keyStone提供的是具有访问范围的令牌同时返回给用户，

Policy：一个基于规则的身份认证引擎，通过配置文件来定义各种动作与用户角色的匹配关系。

### KeyStone的访问流程：



KeyStone在用户和服务之间架起了一道桥梁：用户从KeyStone获取令牌以及服务列表；用户访问服务时，发送自己的令牌；相关的服务向KeyStone求证令牌的合法性。



#### 下面以创建虚拟机为例向大家重新描述这个流程：

![Keystone-workflow](https://gitee.com/hanstack/hanstack_image/raw/master/image/Keystone-workflow.png)

1.用户发送自己的凭证到keystone，keystone认证成功之后返回给用户一个unscoped token以及服务目录。

2.用户通过unscoped token向keystone查询当前环境下的项目列表，keystone进行验证token成功后返回一个项目列表给用户。

3.用户选择一个项目，发送自己的凭证给keyston申请一个scoped token，Keystone验证完成后返回一个scoped token.

4.用户凭借scoped token发送请求到计算服务的Endpoint创建虚拟机，keystone验证scoped token成功后，再把请求发送给Nova，最终创建虚拟机。



### keystone架构

![openstack-architecture-13-638](https://gitee.com/hanstack/hanstack_image/raw/master/image/openstack-architecture-13-638.jpg)