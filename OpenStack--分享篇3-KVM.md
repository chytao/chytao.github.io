---
title: "OpenStack  分享篇3 KVM"
date: 2020-05-01T16:20:42+08:00
draft: true
---

网站：https://www.redhat.com/en/topics/virtualization/what-is-KVM

### KVM的历史

#### 以色列的创业公司Qumranet创建了KVM

​	早期的开发者Avi Kivity

​	2006年10月，在完成基本功能，动态迁移，主要的性能优化后，正式宣布了KVM的诞生。

​	2007年2月发布的linux内核2.6.20中，正式包括了KVM

​	2008年9月Redhat公司以1.7亿美元收购

​	RHEL5.4，在集成5.4的基础上，又将KVM添加进来

​	2011年11月，RHEL6使用KVM彻底替换了Xen

### QEMU

​	QEMU是一个通用的开源的硬件模拟器能够模拟多种硬件

​	QEMU-KVM从分支到主干合并，QEMU成为KVM在用户控件的管理工具

### KVM架构

KVM是内核的一个模块kvm.ko，用户通过QEMU模拟器模拟硬件提供给虚拟机使用，一台虚拟机表现为一个普通的linux进程，通过对这个进程的管理，实现对虚拟机的管理。

![image-20200501085241561](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200501085241561.png)

因为对虚拟机的进程管理较为麻烦，RedHat发布了一个开源项目Libvirt。Libvirt有API，也有一套命令行工具，可以完成对虚拟机的管理，大多数的管理平台都是通过Libvirt来完成对KVM虚拟机的管理，比如openstack，cloudstack，opennebula等。

### QEMU与KVM

QEMU是一个开源项目，实际上是一台硬件模拟器，可以模拟许多硬件，包括X86架构的处理器,AMD64架构的处理器，MIPS R4000，ARMv6/v7，SPARC sun3与PowerPC架构，还支持其他架构。

![image-20200501132345855](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200501132345855.png)

QEMU是纯软件模拟，速度相较于其他的模拟器慢。QEMU和KVM一般配合使用。KVM是一个加载在内核中的软件，本身体量较小，大概只有2万行代码，所以只能完成对CPU和内存的模拟。其他的硬件一般是通过QEMU来完成的。

## Libvirt

![image-20200501153929365](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200501153929365.png)

Vish 命令行管理

Virt-manager 虚拟机图形管理器

​	创建/管理虚拟机

​	创建/虚拟机网络管理

​	创建/虚拟机存储管理

virt-viewer 通过VNC连接到虚拟机的图形显示

virt-install通过命令行创建新的虚拟机



引用--https://wiki.debian.org/libvirt



### 虚拟化的本质

#### 	分区

​		在单一物理服务器上同时运行多个虚拟机

#### 	隔离

​		在同一服务器上运行的多个虚拟机之间相互独立，互不影响

#### 	封装

​		![image-20200501140319892](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200501140319892.png)

​		整个虚拟机都保存在文件中，而且可以通过移动和复制这些文件的方式来移动和复制该虚拟机。

#### 	相对于硬件独立

​		无需修改即可在任何服务器上运行虚拟机

### UVP虚拟化

![image-20200501150212463](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200501150212463.png)

最底层的CPU 内存 网卡 存储等硬件设备；通过自身实现硬件辅助虚拟化。上图中的intel的VT-x是CPU虚拟化的一种方式。Memory是通过EMT技术实现内存的虚拟化，包括网卡上的SR-IOV。

在虚拟监控层hypervison或者VMM的这一层，在这一层中。CPU的虚拟化也就是CPU-->vCPU 的过程以及Memory-->vMemory的过程都是由KVM内核来实现的。KVM是一个软件，但是当它安装完成后是在内核中的，此时KVM和安装KVM的操作系统一起构成了hypersion层。执行虚拟化的功能。但是由于KVM的架构较为简单，代码量小。导致KVM虚拟化不能完成所有的虚拟化工作，只能将完成上图所示的cpu，内存的虚拟化包括一些调度，中断调优等的工作。

由于上面我们说到，KVM虚拟化不能完成所有的虚拟化功能，那么就需要通过其他的方式去完成一些诸如硬件设备的虚拟化工作例如声卡，网卡，显卡，多种并口设备，多种USB设备，多种串口设备，PC喇叭等设备。在众多的虚拟化技术当中由于QEMU能够完成相当多数量的硬件虚拟化能力所以适合配合kvm完成整个的虚拟化功能。在这里需要说明的是，QEMU也是能够支持包括CPU内存的虚拟化的。至于为什么不完全使用QEMU来完成整个虚拟化功能？原因在于QEMU所提供的是完全软件方式来实现的虚拟化能力，速度相对较慢不能利用CPU的硬件辅助虚拟化技术，因为QEMU是一个软件它处在硬件之上的操作系统上，作为操作系统上的软件。决定了他只能工作在non-root模式。kvm去能够安装在系统内核中工作在root模式。QEMU能够支持多种硬件的虚拟化，KVM能够实现高效率的CPU和内存的虚拟化。两者相互配合，由工作在non-root模式的QEMU实现一些外围的硬件设备的虚拟化，KVM负责CPU和内存的虚拟化。

Libvirt是RedHat发布的一个开源项目。Libvirt有API也有一套命令行工具，可以完成对虚拟机的管理。大多数虚拟管理平台都是通过Libvirt来完成对KVM虚拟机的管理的，比如OpenStack，CloudStack等。支持Java，Python,C等主流编程语言。

https://libvirt.org/



## 内存虚拟化

### 	KSM（kernel SamePage Merging)

​		原理类似于软件压缩，将相同的内存分页进行合并。一般用于内存超分配，实现大于物理规格内存的使用。华为叫内存复用技术。

​	内存气泡技术（有资料称之为内存气球技术）：

​	在使用时虚拟机需要安装virtballoon的驱动。

​	balloon技术有两种操作

​		膨胀：虚拟机的内存拿掉给宿主机（VMM）

​		压缩：宿主机（VMM）的内存还给虚拟机

### KVM的优势：

1.KVM是一个开源项目，一直保持开放的姿态，许多虚拟化技术都是在KVM上应用，然后在推广到其他的虚拟化引擎上。

2.性能上KVM能够在硬件条件相当的情况下提供更强的虚拟化能力，因为KVM架构简单，代码量较少，并且原生支持硬件辅助虚拟化技术。

3.KVM是一个开源项目，绝大多数的KVM的解决方案都是免费方案。

4.KVM拥有广泛的技术支持，在KVM的开源社区中众多的支持者像KVM提供技术支持，不断的完善KVM。

