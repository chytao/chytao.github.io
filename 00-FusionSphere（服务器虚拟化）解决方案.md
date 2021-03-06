# 00-FusionSphere（服务器虚拟化）解决方案

## 00-FusionSphere简介

FusionSphere解决方案通过在服务器上部署虚拟化软件，使一台物理服务器可以承担多台服务器的工作。通过整合现有的工作负载并利用剩余的服务器以部署新的应用程序和解决方案，实现较高的整合率。

## 01-FusionSphere解决方案的应用场景

- 单虚拟化场景：

  单虚拟化场景适用于企业只采用FusionCompute作为统一的操作维护管理平台对整个系统进行操作与维护的应用场景。包含资源监控、资源管理、系统管理等。

  FusionCompute主要负责硬件资源的虚拟化，以及对虚拟资源、业务资源、用户资源的集中管理。它采用虚拟计算、虚拟存储、虚拟网络等技术，完成计算资源、存储资源、网络资源的虚拟化。同时通过统一的接口，对这些虚拟资源进行集中调度和管理，从而降低业务的运行成本，保证系统的安全性和可靠性。

- 多虚拟化场景：

  多虚拟化场景适用于企业有多套虚拟化环境需要进行统一管理。多虚拟化场景提供如下主要功能：

  - 统一管理和维护：支持同时接入FusionCompute和VMware虚拟化环境，对多虚拟化环境的资源和业务进行统一的管理和维护。
  - 统一监控告警：支持对多个虚拟化环境、多种物理设备的告警进行统一接入、监控和管理。

- 私有云场景：

  私有云场景适用于企业各部门需要各自管理虚拟资源及业务。发放业务时由管理员和租户分别完成不同的任务，共同完成业务的发放。管理资源时，管理员可以对系统所有资源进行管理；租户只能管理所属VDC的资源。

  根据实际使用需求的不同，私有云场景又可分为多租户共享VPC场景和多租户私有VPC场景。

  - 多租户共享VPC场景

    该场景中，发放业务时管理员通过VDC将虚拟资源分给租户。租户在租户视图，创建虚拟机或应用实例时可使用管理员在共享VPC中创建的网络。若在创建VDC时，将管理员加入了VDC中，则管理员一人在不同视图中，可完成所有任务。该场景适用于如下情况：

    - 企业各部门需要各自管理除网络之外的虚拟资源（包含虚拟机、磁盘等）及业务。
    - 企业中的网络由管理员统一规划和维护，企业各部门均使用管理员创建的网络。

  - 多租户私有VPC场景

    该场景中，发放业务时管理员通过VDC将虚拟资源分给租户。租户在租户视图，自行创建VPC、网络、虚拟机或应用实例。当同时有共享网络需求时，可以由管理员创建共享VPC供所有租户使用。若在创建VDC时，将管理员加入了VDC中，则管理员一人在不同视图中，可完成所有任务。该场景适用于如下情况：

    - 企业各部门需要各自管理虚拟资源（包含网络、虚拟机、磁盘等）及业务。
    - 企业各部门的网络相互隔离，各自规划、创建和维护。

## 02-FusionSphere服务器虚拟化解决方案的价值

华为FusionSphere是业界领先的服务器虚拟化解决方案，能够帮助客户带来如下的价值，从而大幅提升数据中心基础设施的效率。

- 帮助客户提升数据中心基础设施的资源利用率。
- 帮助客户成倍缩短业务上线周期。
- 帮助客户成倍降低数据中心能耗。
- 利用虚拟化基础设施的高可用和强恢复能力，实现业务快速自动化故障恢复，降低数据中心成本和增加系统应用的正常运行时间。

FusionSphere解决方案通过在服务器上部署虚拟化软件，使一台物理服务器可以承担多台服务器的工作。通过整合现有的工作负载并利用剩余的服务器以部署新的应用程序和解决方案，实现较高的整合率。

## 03-FusionCompute产品定位

FusionCompute是云操作系统软件

- 负责硬件资源的虚拟化，以及
- 对虚拟资源、业务资源、用户资源的集中管理。它
- 采用虚拟计算、虚拟存储、虚拟网络等技术，完成计算资源、存储资源、网络资源的虚拟化。
- 通过统一的接口，对这些虚拟资源进行集中调度和管理，从而降低业务的运行成本，保证系统的安全性和可靠性，协助运营商和企业构筑安全、绿色、节能的云数据中心能力。

![image-20200829195401740](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-2020082919540FC定位.png)

## 03-FusionManager产品定位

FusionManager提供服务管理、服务自动化、资源和服务保证等功能。

![image-20200829200739470](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-2020082920073FusinManger0.png)

- 从底层接入来看，FusionManager可以接入物理资源、华为和第三方的虚拟化软件、桌面云、云存储和各种云服务。其中物理资源包括计算设备、存储设备和网络设备。
- 从上层提供的功能来看，FusionManager可以为用户提供ITaaS的多样化的功能和体验。
- 从FusionManager本身提供的功能来看，FusionManager提供了虚拟和物理资源接入、资源自动化管理及资源的可用性和安全性保障等。

因此，FusionManager的定位是以云服务自动化管理和资源智能运维为核心，为用户带来“敏捷、精简”的云数据中心管理体验。

## 05-FusionSphere逻辑架构

![image-20200829200906426](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-2020082920090FusionSphere逻辑架构.png)

- 组件说明：

| 部件              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| FusionCompute     | 必选部件FusionCompute是云操作系统软件，主要负责硬件资源的虚拟化，以及对虚拟资源、业务资源、用户资源的集中管理。它采用虚拟计算、虚拟存储、虚拟网络等技术，完成计算资源、存储资源、网络资源的虚拟化。同时通过统一的接口，对这些虚拟资源进行集中调度和管理，从而降低业务的运行成本，保证系统的安全性和可靠性，协助运营商和企业构筑安全、绿色、节能的云数据中心能力。VSA是提供虚拟防火墙、DHCP功能的系统服务虚拟机，仅当用户使用FusionManager提供的网络高级特性时安装。 |
| FusionManager     | 可选部件FusionManager主要对云计算的软件和硬件进行全面的监控和管理，实现同构，异构VMware虚拟化多资源池管理；软硬件统一告警监控；自助服务及流程审批三大关键能力，并向内部运维管理人员提供运营与管理门户。 |
| FusionSphere eSDK | 可选部件FusionSphere eSDK是FusionSphere服务器虚拟化北向统一接口，第三方网关系统和其他运营平台可以通过eSDK轻松完成无缝对接。eSDK开放了FusionSphere服务器虚拟化的全部能力，包括虚拟机的生命周期管理，虚拟化高级功能，运维管理功能等。 |
| FusionSphere SOI  | 可选部件FusionSphere SOI性能监控和分析系统，用来对FusionSphere云计算系统中虚拟机的性能环境指标进行采集和展示，建立模型进行分析，根据历史和当前数据对未来性能变化进行预测，从而给出对管理员的系统性能管理建议。 |
| eBackup           | 可选部件eBackup是虚拟化备份软件，配合FusionCompute快照功能和CBT（Changed Block Tracking）备份功能实现FusionSphere的虚拟机数据备份方案。 |
| UltraVR           | 可选部件UltraVR是容灾业务管理软件，利用底层SAN存储系统提供的异步远程复制特性，为华为FusionSphere提供虚拟机关键数据的数据保护和容灾恢复。 |

## 06-FusionCompute逻辑架构

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-2020.png)

| 模块                                             | 功能                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| CNA（Compute Node Agent）：计算节点代理          | CNA主要提供以下功能：提供虚拟计算功能。管理计算节点上的虚拟机。管理计算节点上的计算、存储、网络资源。 |
| VRM（Virtual resource management）：虚拟资源管理 | VRM主要提供以下功能：管理集群内的块存储资源。通过DHCP（Dynamic Host Configuration Protocol）为虚拟机分配私有IP地址。管理集群内的网络资源(IP/VLAN/DHCP)，为虚拟机分配IP地址。管理集群内虚拟机的生命周期以及虚拟机在计算节点上的分布和迁移。管理集群内资源的动态调整。通过对虚拟资源、用户数据的统一管理，对外提供弹性计算、存储、IP等服务。通过提供统一的操作维护管理接口，操作维护人员通过WebUI远程访问FusionCompute对整个系统进行操作维护，包含资源管理、资源监控、资源报表等。 |

## 07-FusionCompute的产品功能

### 虚拟化计算：

#### 服务器虚拟化:

将服务器物理资源抽象成逻辑资源，让一台服务器变成几台甚至上百台相互隔离的虚拟服务器，不再受限于物理上的界限，而是让CPU、内存、磁盘、I/O等硬件变成可以动态管理的“资源池”，从而提高资源的利用率，简化系统管理。同时硬件辅助虚拟化技术提升虚拟化效率，增加虚拟机的安全性。

##### 计算虚拟化的分类：

- 裸金属虚拟化（I型虚拟化）

  FusionCompute的Hypervisor使用裸金属架构，直接在硬件上安装虚拟化软件，将硬件资源虚拟化。由于使用了裸金属架构，FusionCompute可为用户带来接近服务器性能、高可靠和可扩展的虚拟机。

  ![image-20200829202112138](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829202112弟.png)

- 寄居虚拟化（II型虚拟化）

  最底层是物理硬件，物理硬件之上是主机的操作系统，操作系统之上是VMM（Virtual machine monitor：虚拟机管理层）；在VMM上才是客户虚拟机

  ![image-20200829202134045](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-2020082920213而型虚拟化.png)

所谓的I型虚拟化和II型虚拟化我认为其实最根本的区别在于虚拟化所在的层级不同。I型虚拟化实现虚拟化的层在系统层。II型虚拟化实现虚拟化的层在应用层。这两个层级分别对应了内核态和用户态。那么什么是内核态？什么是系统态？我们引入CPU的虚拟化一起来讲。

##### CPU虚拟化

在虚拟化发展的早期主要以*全虚拟化*和*半虚拟化*两大流派为主，两者各有优缺点。如果能适当的将其应用不同的环境中，就能充分的发挥两者的特性以获得更高的收益。随着这两大流派的分歧和竞争愈演愈烈，由Intel领衔的硬件厂商也纷纷加入到虚拟化的浪潮中，开启了(大航海时代)硬件虚拟化的时代，从此全虚拟化和半虚拟化前进的道路逐渐有了靠拢的趋势。再到后来的 *第二代的内存虚拟化*、*第三代的总线虚拟化* 的出现和兴起，当下虚拟化市场已经不再以单纯卖卖虚拟化软件为主要盈利手段，而是将虚拟化技术整合在更大、更完善的虚拟化平台解决方案中。其中包括Redhat的RHEV、VMWare的vSphere等。

CPU的复用比不建议超过1:4

**x86 CPU架构与虚拟化之间的关系**

在理解各种虚拟化的实现之前，首先要了解x 86cpu的架构



![](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200430002433824.png)

**注意**：CPU为了保证程序代码执行的安全性、多用户的独立性、保护OS的正常运行，提出了CPU执行状态的概念。这样能够限制不同程序之间的访问能力，避免一个程序获取另一个程序的内存数据造成数据混乱，同时也避免了程序错误的操作物理硬件。一般CPU都会划分为 *用户态* 和 *内核态* ，x86的CPU架构更是细分为了Ring3~0四种状态。

**Ring3 用户态(User Mode)**：运行在用户态的程序代码需要受到CPU的检查，用户态程序代码只能访问 *内存页表项中* 规定能被用户态程序代码访问的页面虚拟地址(受限的内存访问)，而且还只能访问 *TSS中的I/O Permission Bitmap* 中规定能被用户态程序代码访问的端口。甚至不能直接访问外围硬件设备、不能抢占CPU。所有的应用程序(Application)都运行在用户态上。——*当运行在用户态的Application需要调用只能被核心态代码直接访问的硬件设备时，CPU会通过特别的接口去调用核心态的代码，以此来实现Application对硬件设备的调用。* 如果用户态的Application直接调用硬件设备时，就会被Host OS捕捉到并触发异常，弹出警告窗口。

**Ring0 核心态(Kernel Mode)**：是Host OS Kernel运行的模式，运行在核心态的代码可以无限制的对系统内存、设备驱动程序、网卡接口、显卡接口等外围硬件设备进行访问。只有Host OS能够无限制的访问磁盘、键盘等外围硬件设备的数据，但是首先需要在Host OS上安装驱动程序。

**虚拟化实现**

![image-20200829204404253](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-202008292044虚拟化实现3.png)

简单来说：Guest OS和VMM都属于运行在Ring3上的应用程序或者是一个进程。GuestOS操作硬件设备时并执行操作指令时，VMM会将该操作指令监控、捕获、检测后将指令传递给HostOS，HostOS会将GuestOS发出的运行于用户态的操作指令模拟为核心态指令。 
**注意**：当上述的流程是非常简略的，在与全虚拟化和半虚拟化的实现过程集合时，就会变得非常复杂。

![image-20200829204729948](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829204进程8.png)

**全虚拟化（Full virtualization）**

GuestOS可以直接在全虚拟化VMM上运行而不需要对GuestOS本身的核心代码做任何修改，全虚拟化的GuestOS具有完全的物理机特性。既VMM会为GuestOS抽象模拟出它所需要的包括CPU、磁盘、内存、网卡、显卡等抽象硬件资源，所以全虚拟化的GuestOS并不会知道自己其实是一台虚拟机。

![image-20200829205435483](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829205435483.png)

**结合上述的虚拟化实现图来看**：当我们使用GuestOS的时候，不可避免的会调用GuestOS中的 *虚拟设备驱动程序*和 *核心调度程序* 来操作硬件设备。与HostOS的不同在于，HostOS运行在CPU的核心态中，这就表示HostOS可以直接对硬件设备进行操作。但GuestOS作为一个运行在CPU用户态中应用程序，不能够直接的操作硬件设备。为了解决这个问题，VMM引用了两个机制——*特权解除 & 陷入模拟*。

**特权解除**：也称之为 *翻译*，当GuestOS需要调用运行在核心态的指令时，VMM就会动态的将核心态指令捕获并调用若干运行在非核心态的指令来模拟出期望得到的效果(GuestOS和VMM是运行在用户态上的应用程序)，从而将核心态的特权解除。解除了核心态的特权后，就能够在GuestOS中执行大部分的核心态指令了。但是，这仍然不能完美的解决问题。因为在一个OS的指令集中还存在着一种敏感指令(可能是内核态，也可能是用户态)。此时就需要陷入模拟的实现。

**陷入模拟**：无论是HostOS还是GuestOS，只要是一个OS都必然会存在有敏感指令(reboot、shutdown等)。试想如果我们希望将GuestOS重启，并在GuestOS中执行了`reboot`指令，但是却将HostOS给重启了，这将会非常糟糕。VMM的陷入模拟机制就是为了解决这个问题。**e.g.** 在GuestOS中执行了敏感指令`reboot`时，VMM首先会将敏感指令`reboot`捕获、检测并判定其为敏感指令。此时VMM就会陷入模拟，将敏感指令`reboot`模拟成一个只针对GuestOS进行操作的、非敏感的、并且运行在非核心态上的`"reboot"`指令，最后CPU执行虚拟机的重启操作。

由于全虚拟化VMM会频繁的捕获这些核心态的和敏感的指令，将这些指令进行转换之后，再交给CPU执行。所以 *经过了两重转换*，导致其效率会比半虚拟化更低，但全虚拟化VMM应用程序的好处在于其不需要对GuestOS的核心源码做修改，所以全虚拟化的VMM可以安装绝大部分的OS(暂时来说只有已Linux、open soralis、BSD等几种OS开源了内核代码)。典型的全虚拟化软件有 —— VMWare、Hyper-V、KVM-x86(复杂指令集)。

**全虚拟化的两种实现方式**： 

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/wKioL1afFnSRpxlQAABjQiFvejo026.png)

- .基于陷入模拟的全虚拟化 

最先实现这种CPU全虚拟化技术的是Trap-and-emulation技术，即**陷入模式和模拟仿真**技术。这种技术通过将OS需求的特权指令通过VMM自动捕获的方式运行后返回去OS。当OS有特权指令产生时，VMM将其自动捕获，将OS所请求的特权指令进行截获，然后通过VMM运行之后将结果返回给OS层。VMM会使用模拟仿真将特权指令模拟仿真的方式执行一遍。

![wKiom1afFk-T2kDjAACQyVyCch0212.png](https://gitee.com/hanstack/hanstack_image/raw/master/image/wKiom1afFk-T2kDjAACQyVyCch0212.png)

在虚拟化模式下，就存在着2中特殊的指令：特权指令和敏感指令。那么什么是特权指令？什么是敏感指令？

**特权指令：**系统中有一些操作和管理关键系统资源的指令，这些指令只有在最高特权级上能够正确运行。如果在非最高特权级上运行，特权指令会引发一个异常，处理器会陷入到最高特权级，交由系统软件处理了。

**敏感指令：**操作特权资源的指令，包括修改虚拟机的运行模式或者下面物理机的状态；读写时钟、中断等寄存器；访问存储保护系统、地址重定位系统及所有的I/O指令。

根据Popek和Goldberg的定义，指令集支持虚拟化的前提是：所有敏感指令都是特权指令。很可惜x86指令集不能满足这个要求。

虚拟化场景下，要求将GuestOS内核的特权解除，从原来的0降低到1或者3。这部分特权指令在Guest OS中发生的时候，就会产生Trap，被VMM捕获，从而由VMM完成。这就是虚拟的本质方法，**特权解除和陷入模拟(Privilege deprivileging/Trap-and-Emulation)**。虚拟化场景中敏感指令必须被VMM捕获并完成。对于一般 RISC 处理器，如 MIPS，PowerPC 以及SPARC，敏感指令肯定是特权指令，但是x86 例外，x86绝大多数的敏感指令是特权指令，但是由于部分敏感指令不是特权指令，执行这些指令的时候不会自动trap被VMM捕获。

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/922424-20170722203933637-947779848.png)

- 基于二进制翻译的全虚拟化 

  采用模拟仿真的方式模拟和虚拟化x86架构的CPU，但是由于x86架构的CPU中，不是所有的敏感指令都是特权指令，所以并不能完全的解决掉那些不是特权指令的敏感指令的模拟仿真问题。例如SGDT, SLDT, SIDT …

  由于模拟仿真技术固有的缺陷，导致对CPU的虚拟化并不完整。所以也导致了基于x86的虚拟化难以和其他CPU架构一样实现虚拟化。比如IBM的Power CPU架构就很早具备了虚拟化的技术并使用于实践。

  这个现象在1999年得到改善，VMware通过二进制翻译技术完成了对x86 CPU架构的完全虚拟化。

  其主要采用**优先级压缩技术（Ring Compression）**和**二进制代码翻译技术（Binary Translation）**。优先级压缩技术让VMM和Guest运行在不同的特权级下。对x86架构而言，即VMM运行在最高特权级别Ring 0下，Guest OS运行在Ring 1下，用户应用运行在Ring 3下。因此，Guest OS的核心指令无法直接下达到计算机系统硬件执行，而是需要经过VMM的捕获和模拟执行（部分难以虚拟化的指令需要通过二进制翻译【Binary Translation】技术进行转换）。

  ![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/wKioL1afFsDjjbbTAAAl_Q4fU1I877.png)

  二进制翻译技术简称BT，是一种直接翻译可执行二进制程序的技术，能够把一种处理器上的二进制程序翻译到另外一种处理器上执行。二进制翻译技术将机器代码从源机器平台映射(翻译)至目标机器平台，包括指令语义与硬件资源的映射，使源机器平台上的代码“适应”目标平台。因此翻译后的代码更适应目标机器，具有更高的运行时效率。二进制翻译系统是位于应用程序和计算机硬件之间的一个软件层，它很好地降低了应用程序和底层硬件之间的耦合度，使得二者可以相对独立地发展和变化。二进制翻译也是一种编译技术，它与传统编译的差别在于其编译处理对象不同。传统编译处理的对象是某一种高级语言，经过编译处理生成某种机器的目标代码；二进制翻译处理的对象是某种机器的二进制代码，该二进制代码是通过传统编译过程生成的，经过二进制翻译处理后生成另一种机器的二进制代码。

  根据不同的实现方式，二进制翻译技术可分为三大类：解释执行，静态翻译和动态翻译。

   **代码解释执行**

  解释执行(Interpretation)过程对源机器代码中的每条指令实时解释执行，系统不保存且不缓存解释过的指令，不需要用户干涉，也不进行任何优化。解释器相对容易开发，比较容易与老的体系结构高度兼容，但效率很差。

   **静态二进制翻译**

  在静态二进制翻译(SBT，Static BinaryTranslation)中，代码在运行之前被离线翻译，根据目标机器的指令结构生成一个新的程序，然后直接执行这个翻译后生成的程序。静态翻译器的离线翻译过程不会给程序运行带来额外开销，因此可以充分采用各种优化措施生产高质量代码，大大提高运行时效率。

   **动态二进制翻译**

  动态二进制翻译(DBT，Dynamic BinaryTranslation)则在程序运行时对执行到的代码片段进行翻译，克服了静态翻译所无法解决的一些困难，如运行时动态信息收集，代码挖掘，自修改代码和精确中断问题。而且动态翻译器对用户完全透明，无需用户干预。虽然动态翻译有上述诸多优点，翻译过程却由于受到动态执行的限制而不能像静态翻译那样进行完全细致的优化，使得翻译生成的代码效率比静态翻译器差。

  三种二进制翻译技术的比较：

  | 类别     | 优点                                                         | 缺点                                       |
  | -------- | ------------------------------------------------------------ | ------------------------------------------ |
  | 解释执行 | 开发容易，不需要用户干预，高度兼容                           | 效率很差                                   |
  | 静态翻译 | 离线翻译，可以进行更好的优化，效率较高                       | 依赖解释器、运行环境支持；需要终端用户参与 |
  | 动态翻译 | 无需解释器和运行环境的支持，无需用户参与，利用动态信息来发现优化的机会 | 翻译代码效率不高，对目标机器有额外的开销   |

  解释执行是最易实现的一种翻译技术，但是其繁琐的实现方式大大降低了翻译系统的执行效率。静态翻译虽然能提供高效的运行时性能，但由于无法在静态环境下覆盖所有代码，无法脱离对解释器的依赖。与上述两种相比，动态翻译很好的解决了代码覆盖、自修改代码和精确中断等诸多问题，同时也能提供可接受的执行效率。因此VMware基于动态二进制翻译技术实现了x86架构的CPU的虚拟化。

  

  典型动态二进制翻译：被翻译的代码称为源机器代码，在宿主机上运行的代码称为目标机器代码，一个典型的动态二进制翻译器主要包括两个模块：翻译引擎和执行引擎。其中翻译器引擎负责将源机器代码翻译代码翻译成目标机器代码；执行引擎负责准备目标机器代码运行的上下文环境（Execution Context）然后从目标机器代码缓存中找到源机器代码对应的目标代码并执行。

  ![image-20200829213147108](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829213147108.png)

  其基本运行流程如下：

   **查找(Lookup)阶段**

  这个阶段查询目标代码块是否存在于目标代码缓存中，如果存在则返回目标块入口地址，如果不存在则进入翻译阶段。

   **上下文切换(Context Switch)阶段**

  当一个目标代码块被查询到或者翻译模块生成的时候，二进制翻译系统会执行一次控制权转移。系统会把控制权交给执行模块去运行该目标代码块，目标代码块运行完毕后系统需要恢复执行引擎的控制权。一次控制权转移需要保存程序的上下文环境。

   **翻译(Translation)阶段**

  完成从源机器二进制代码到目标机器二进制代码的翻译。包括解码、中间代码优化、编码三个子阶段。

   **执行和链接(Executing & Linking)阶段**

  当基本块被翻译生成目标代码块之后，依照源代码的控制流完成目标代码块之间的直接以及间接跳转的链接，并依次运行目标代码块。

**半虚拟化 Paravirtualization**

![image-20200829205540340](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829205540340.png)

半虚拟化是需要GuestOS协助的虚拟化。因为在半虚拟化VVMM中运行的GuestOS，都需要将其内核源码进行都进过了特别的修改。半虚拟化VMM在处理敏感指令和内核态指令的流程上相对更简单一些。在半虚拟化VMM上运行的GuestOS都需要修改内核代码，主要是修改GuestOS指令集中的敏感指令和核心态指令。让HostOS在捕抓到没有经过半虚拟化VMM模拟和翻译处理的GuestOS内核态指令或敏感指令时，HostOS也能够准确的判断出该指令是否属于GuestOS(GuestOS知道自己是虚拟机)。这样就可以高效的避免了上述问题。典型的半虚拟化软件有——Xen、KVM-PowerPC(简易指令集)

半虚拟化除了修改内核外还有另外一种实现方法——在每一个GuestOS中安装半虚拟化软件，**e.g.** VMTools、RHEVTools。

**注意**：若使用KVM运行Windows时，一定要安装半虚拟化驱动Tools，否则无法工作。现在主流的半虚拟化驱动是由IBM和redhat联合开发一个通用半虚拟机驱动virtio 。

对于x86体系结构CPU，Xen使用超级调用来替换被监控的操作，其中包括x86架构下的敏感指令。Xen所采用的超级替换的方法是一种全新的设计理念：它将问题的中心，由VMM移向Guest OS自身，通过主动的方式由Guest OS去处理这些指令，而不是被移交给VMM做处理，在这种设计理念下，修改Guest OS内核。

  能修改Guest OS是半虚拟化的一个技术核心。通过修改Guest OS的内核。使Guest OS明确知道自己是运行在1环上，而不是通常OS的0环，有效的避免了虚拟化的执行冲突问题。Guest OS也清楚VMM给自己提供了一个虚拟的寄存器组，并能通过其他方式去访问他们，避免了访问冲突的问题。

  解决了敏感指令问题只是解决了x86架构下的半虚拟化的第一步。运行在1环的操作系统没有权限执行的指令，交给0环的VMM来处理，这个很大程度上与应用程序的系统调用很类似：系统调用的作用是把应用程序无权执行的指令交给操作系统完成。因此，Xen向Guest OS提供了一套“系统调用”。以方便Guest OS调用，这部分”系统调用“就是超级调用Hypercall。

超级调用Hypercall的机制使用，不仅使x86架构的指令虚拟化得以实现，也为后面的内存虚拟化和I/O虚拟化提供了新的思路和方法，超级调用和事件通道是整个半虚拟化的基础。

![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/wKiom1afF9-BZbZuAAAotW_0zjg092.png)

**X86架构特权级**

x86 硬件支持 4 个特权级 (Ring)，一般内核运行在 Ring 0， 用户应用运行在 Ring 3， 更小的 Ring 有比更高的 Ring 能访问更多的系统全局资源，即更高的特权。有些指令只能在 Ring 0 才能正确执行，如 LGDT、LMSW 指令，我们称之为特权指令；另外有些指令可以在 Ring 3 正确执行，如 SGDT、 SMSW、PUSHF/POPF，我们称之为非特权指令。

正常模式和虚拟化两种情况叙述下，特权模式说明如下：

**正常模式**：特权级别是针对段来讲的，段描述符的最后两位标识了该段所位于的特权级别，比如，中断处理程序运行于ring0（），此时的内核程序是具有特权的，即ring0。位于ring3用户程序可以通过系统调用的方式，int80，后特权翻转入ring0，然后就可以顺利执行中断处理程序（好像是用户程序调用内核程序的唯一途径）。

**虚拟化情况下**：

**特权解除**：是指解除正常情况下运行于ring0的段，比如中断处理程序，为了虚拟化需要，此时解除其特权，将其运行于ring1。当用户程序通过系统调用时，其跳转到的中断处理程序运行于ring1。但是，在中断处理程序中，有部分指令是必须在ring0才能执行的，此时，便会自动陷入，然后模拟。也就是说，用户程序运行特权指令，会有两次特权下降，一次是通过系统调用进入ring1，第二次是通过特权指令陷入进入ring0。这说明，中断发生时的中断处理程序还是以前的位于内核的代码，但是其运行级别为ring1，部分指令还需要再次陷入，才能执行。另外，还有一个重要问题，就是部分敏感非特权指令无法陷入的问题：存在二进制翻译、超级调用等方式，强迫其陷入，然后模拟。

在传统的 X86 平台上支持虚拟化上存在如下问题 :

X86 指令集中存在 **17 条敏感的非特权指令**，“非特权指令”表明这些指令可以在 x86 的 ring 3 执行， 而“敏感性”说明 VMM 是不可以轻易让客操作系统执行这些指令的。 这 17 条指令在客操作系统上的执行或者会导致系统全局状态的破坏，如 POPF 指令，或者会导致客操作系统逻辑上的问题，如 SMSW 等读系统状态或控制寄存器的指令。传统的 X86 没法捕获这些敏感的非特权指令。

除了那 17 条敏感的非特权指令，其他敏感的指令都是敏感的特权指令。在 x86 虚拟化环境，VMM 需要对系统资源进行统一的控制，所以其必然要占据最高的特权级，即 Ring 0， 所以为了捕获特权指令，在传统 x86 上一个直接可行的方法是 “Ring deprivileging”， 如将客操作系统内核的特权级从 Ring 0改为 Ring 1 或 Ring 3， 即 “消除” 客内核的特权，以低于 VMM所在的 Ring 0， 从而让 VMM 捕获敏感的特权指令。

**总结**

　　半虚拟化的思想就是，让客户操作系统知道自己是在虚拟机上跑的，工作在非ring0状态，那么它原先在物理机上执行的一些特权指令，就会修改成其他方式，这种方式是可以和VMM约定好的，这就相当于，通过修改代码把操作系统移植到一种新的架构上来，就像是定制化。所以XEN这种半虚拟化技术，客户机操作系统都是有一个专门的定制内核版本，和x86、mips、arm这些内核版本。这样以来，就不会有捕获异常、翻译、模拟的过程了，性能损耗非常低。这就是XEN这种半虚拟化架构的优势。这也是为什么XEN半虚拟化只支持虚拟化Linux，无法虚拟化windows原因，微软不修改代码无法实现半虚拟化。

**硬件辅助虚拟化 HVM**

![image-20200829205602878](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829205602878.png)

*2005年* — Intel提出并开发了由CPU直接支持的虚拟化技术。这种虚拟化技术引入新的CPU运行模式和新的指令集，使得VMM和GuestOS运行于不同的模式下(VMM=Root Mode;GuestOS=Non-Root Mode)，GuestOS运行于受控模式，原来的一些敏感指令在受控模式下会全部陷入VMM，由VMM来实现模拟，这样就解决了部分非内核态敏感指令的陷入——*模拟难题*，而且模式切换时上下文的保存恢复由硬件来完成，这样就大大提高了陷入——*模拟时上下文切换的效率* 。该技术的引入使x86 CPU可以很容易地实现完全虚拟化。故皆被几乎所有之前分歧的各大流派所采用，包括KVM-x86，VMWare ESX Server 3，Xen 3.0 。

**HVM的分类**： 
1). Intel –> VT-X 
2). AMD –> AMD-V

目前主要有Intel的VT-x和AMD的AMD-V这两种技术。其核心思想都是通过引入新的指令和运行模式，使VMM和Guest OS分别运行在不同模式（ROOT模式和非ROOT模式）下，且Guest OS运行在Ring 0下。通常情况下，Guest OS的核心指令可以直接下达到计算机系统硬件执行，而不需要经过VMM。当Guest OS执行到特殊指令的时候，系统会切换到VMM，让VMM来处理特殊指令。

**1、Intel VT-x技术**

为弥补x86处理器的虚拟化缺陷，市场的驱动催生了VT-x，Intel推出了基于x86架构的硬件辅助虚拟化技术Intel VT(Intel Virtualization Technology)。

目前，Intel VT技术包含CPU、内存和I/O三方面的虚拟化技术。

- CPU硬件辅助虚拟化技术，分为对应安腾架构的VT-i(Intel Virtualization Technology for ltanium)和对应x86架构的VT-x(Intel Virtualization Technology for x86)。
- 内存硬件辅助虚拟化技术包括EPT(Extended Page Table)技术。
- I/O硬件辅助虚拟化技术的代表VT-d(Intel Virtualization Technology for Directed I/O)。

Intel VT-x技术解决了早期x86架构在虚拟化方面存在的缺陷，可使未经修改的Guest OS运行在特权级0，同时减少VMM对Guest OS的干预。Intel VT-d技术通过使VMM将特定I/O设备直接分配给特定的Guest OS，减少VMM对I/O处理的管理，不但加速数据传输，且消除了大部分性能开销。如下图所示。CPU硬件辅助虚拟化技术简要说明流程图：

[![wKioL1afGVfxeUomAAAz5_tFJmI638.png](https://gitee.com/hanstack/hanstack_image/raw/master/image/wKio滴滴滴z5_tFJmI638.png)](http://s3.51cto.com/wyfs02/M01/79/F2/wKioL1afGVfxeUomAAAz5_tFJmI638.png)

效法IBM 大型机，VT-x提供了2 个运行环境：根（Root）环境和非根（Non-root）环境。根环境专门为VMM准备，很像原来没有VT-x 的x86，只是多了对VT-x 支持的几条指令。非根环境作为一个受限环境用来运行多个虚拟机。

根操作模式与非根操作模式都有相应的特权级0至特权级3。VMM运行在根模式的特权级0，GuestOS的内核运行在非根模式的特权级0，GuestOS的应用程序运行在非根模式的特权级3。运行环境之间相互转化，从根环境到非根环境叫VMEntry；从非根环境到根环境叫VMExit。VT-x定义了VMEntry操作，使CPU由根模式切换到非根模式，运行客户机操作系统指令。若在非根模式执行了敏感指令或发生了中断等，会执行VMExit操作，切换回根模式运行VMM。

##### 补充：内核态用户态

![](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-202008122306关系.png)

**--->内核态:** CPU可以访问内存所有数据, 包括外围设备, 例如硬盘, 网卡. CPU也可以将自己从一个程序切换到另一个程序
**--->用户态:** 只能受限的访问内存, 且不允许访问外围设备. 占用CPU的能力被剥夺, CPU资源可以被其他程序获取

(2)为什么需要用户态和内核态？

--->由于需要限制不同的程序之间的访问能力, 防止他们获取别的程序的内存数据, 或者获取外围设备的数据, 并发送到网络, CPU划分出两个权限等级 :**用户态** 和 **内核态**

(3)用户态与内核态的切换?

所有用户程序都是运行在用户态的, 但是有时候程序确实需要做一些内核态的事情, 例如从硬盘读取数据, 或者从键盘获取输入等. 而唯一可以做这些事情的就是操作系统, 所以此时程序就需要先操作系统请求以程序的名义来执行这些操作.

这时需要一个这样的机制: 用户态程序切换到内核态, 但是不能控制在内核态中执行的指令

这种机制叫**系统调用（System call）**, 在CPU中的实现称之为**陷阱指令**(Trap Instruction)

**CPU和VCPU对应关系**

![image-20200829215020607](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829215020607.png)

单台服务器有2个物理CPU，每颗CPU有8核，又因为超线程技术可以提供每个物理内核两个处理线程，因此每颗CPU有16线程，总vCPU数量为2*8*2=32个vCPU。总资源为32*2.6GHz=83.2GHz。

虚拟机vCPU数量不能超过单台CNA节点可用vCPU数量。多个虚拟机间可以复用同一个物理CPU，因此单CNA节点上运行的虚拟机vCPU数量总和可以超过实际vCPU数量。

##### 内存虚拟化（x86）

FusionCompute支持内存硬件辅助虚拟化技术，降低内存虚拟化开销，提升约30%的内存访问性能。同时，FusionCompute支持智能内存复用策略，自动优化组合各种内存复用策略，实现内存的高复用率。每个虚拟机最大支持1TB虚拟内存。FusionCompute支持以下内存复用技术：

- 内存气泡：系统主动回收虚拟机暂时不用的物理内存，分配给需要复用内存的虚拟机。内存的回收和分配都是动态的，虚拟机上的应用无感知。整个物理服务器上的所有虚拟机使用的分配内存总量不能超过该服务器的物理内存总量。
- 内存交换：将外部存储虚拟成内存给虚拟机使用，将虚拟机上暂时不用的数据存放到外部存储上。系统需要使用这些数据时，再与预留在内存上的数据进行交换。
- 内存共享：多台虚拟机共享数据内容为零的内存页。

## 08-关键特性

**内存虚拟化问题**

Native操作系统对内存的认识和管理两点共识：

- 内存都是从物理地址0开始的
- 内存都是连续的

内存虚拟化需要解决的两个问题

- 物理地址0只有一个无法满足多个虚拟机的内存地址需要从0开始
- 地址连续：虽然可以分配连续的物理地址，但是内存使用效率不高，缺乏灵活性。

内存虚拟化：把物理机的真实物理内存同一管理，包装成多个虚拟机的聂村给若干虚拟机使用。KVM通过内存虚拟化共享物理系统内存。动态分配给虚拟机。

![image-20200829220039512](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829220039512.png)

在KVM中，虚拟机物理内存为qemu-kvm进程所占用的内存空间。KVM使用CPU辅助的内存虚拟化方式。在Intel平台上，内存虚拟化的实现方式为EPT（Extended page

 Table）

#### 虚拟机资源管理

客户可以通过自定义方式或基于模板创建虚拟机，并对集群资源进行管理，包括资源自行动态调度（包含负载均衡和动态节能）、虚拟机管理（包含创建、删除、启动、关闭、重启、休眠、唤醒虚拟机等）、存储资源管理（包含普通磁盘和共享磁盘的管理）、虚拟机安全管理（包含自定义VLAN等），此外，还可以根据业务负载灵活调整虚拟机的QoS（包括CPU QoS和内存QoS）。

- 虚拟机生命周期管理

  虚拟机支持多种操作方式，用户可根据业务负载灵活调整虚拟机状态。虚拟机操作方式包括：

  - 创建/删除/启动/关闭/重启/查询虚拟机

    FusionCompute接受来自业务管理系统的创建虚拟机请求，依据请求中定义的虚拟机规格（VCPU、内存大小、盘大小）、镜像要求、网络要求等，选择合适的物理资源创建虚拟机，并在虚拟机创建完成之后，查询虚拟机运行状态和属性。在使用虚拟机的过程中，用户可以停止、重启、甚至删除自己的虚拟机。该功能为用户提供了基本的虚拟机操作和管理功能，方便用户对虚拟机的使用。

  - 休眠/唤醒虚拟机

    当业务处于低负载量运行时，可以只保留部分虚拟机满足业务需求，将其他空闲虚拟机休眠，以降低物理服务器的能耗；当需要业务高负载运行时，再将虚拟机唤醒，以满足高负载业务量正常运行需求。该功能满足业务系统对资源需求的灵活性，提高系统的资源利用率。

- 虚拟机模板

  通过使用虚拟机模板功能，用户可对虚拟机定义规格化模板，并使用模板方式完成虚拟机创建。

- CPU QoS

  虚拟机的CPU QoS用于保证虚拟机的计算资源分配，隔离虚拟机间由于业务不同而导致的计算能力相互影响，满足不同业务对虚拟机计算性能的要求，最大程度复用资源，降低成本。

  创建虚拟机时，可根据虚拟机预期部署业务对CPU的性能要求而指定相应的CPU QoS。不同的CPU QoS代表了虚拟机不同的计算能力。指定CPU QoS的虚拟机，系统对其CPU的QoS保障，主要体现在计算能力的最低保障和资源分配的优先级。

  CPU QoS包含如下三个参数：

  - **CPU资源份额**

    CPU份额定义多个虚拟机在竞争物理CPU资源的时候按比例分配计算资源。

    以一个主频为2.8GHz的单核物理主机为例，如果上面运行有三台单CPU的虚拟机。三个虚拟机A，B，C，份额分别为1000，2000，4000。当三个虚拟机CPU满负载运行时，会根据三个虚拟机的份额按比例分配计算资源。份额为1000的虚拟机A的计算能力约为400MHz的，份额为2000的虚拟机B获得的计算能力约为800MHz，份额为4000的虚拟机C获得的计算能力约为1600MHz。（以上举例仅为说明CPU份额的概念，实际应用过程中情况会更复杂）。

    CPU份额只在各虚拟机竞争计算资源时发挥作用，如果没有竞争情况发生，有需求的虚拟机可以独占物理CPU资源，例如，如果虚拟机B和C均处于空闲状态，虚拟机A可以获得整个物理核即2.8GHz的计算能力。

  - **CPU资源预留**

    CPU预留定义了多个虚拟机竞争物理CPU资源的时候分配的最低计算资源。

    如果虚拟机根据份额值计算出来的计算能力小于虚拟机预留值，调度算法会优先按照虚拟机预留值的能力把计算资源分配给虚拟机，对于预留值超出按份额分配的计算资源的部分，调度算法会从主机上其他虚拟机的CPU上按各自的份额比例扣除，因此虚拟机的计算能力会以预留值为准。

    如果虚拟机根据份额值计算出来的计算能力大于虚拟机预留值，那么虚拟机的计算能力会以份额值计算为准。

    以一个主频为2.8GHz的单核物理机为例，如果运行有三台单CPU的虚拟机A、B、C，份额分别为1000、2000、4000，预留值分别为700MHz、0MHz、0MHz。当三个虚拟机满CPU负载运行时：

    - 虚拟机A如果按照份额分配，本应得400MHz，但由于其预留值大于400MHz，因此最终计算能力按照预留值700MHz算。
    - 多出的(700-400)MHz按照B和C各自的份额比例从B和C处扣除。
    - 虚拟机B获得的计算能力约为(800-100)MHz，虚拟机C获得的计算能力约为(1600-200)MHz。

    CPU预留只在各虚拟机竞争计算资源的时候才发挥作用，如果没有竞争情况发生，有需求的虚拟机可以独占物理CPU资源。例如，如果虚拟机B和C均处于空闲状态，虚拟机A可以获得整个物理核即2.8GHz的计算能力。

  - **CPU资源限额**

    控制虚拟机占用物理CPU资源的上限。以一个两CPU的虚拟机为例，如果设置该虚拟机CPU上限为3GHz，则该虚拟机的两个虚拟CPU计算能力被限制为1.5GHz。

- 内存QoS

  提供虚拟机内存智能复用功能，依赖内存预留比。通过内存气泡等内存复用技术将物理内存虚拟出更多的虚拟内存供虚拟机使用，每个虚拟机都能完全使用分配的虚拟内存。该功能可最大程度的复用内存资源，提高资源利用率，且保证虚拟机运行时至少可以获取到预留大小的内存，保证业务的可靠运行。

  系统管理员可根据用户实际需求设置虚拟机内存预留。内存复用的主要原则是：优先使用物理内存。

  内存QoS包含如下两个参数：

  - **内存资源份额**

    内存份额定义多个虚拟机竞争内存资源的时候按比例分配内存资源。

    在虚拟机申请内存资源，或主机释放空闲内存（虚拟机迁移或关闭）时，会根据虚拟机的内存份额情况按比例分配。

    不同于CPU资源可实时调度，内存资源的调度是平缓的过程，内存份额策略在虚拟机运行过程中会不断进行微调，使虚拟机的内存获取量逐渐趋于比例。

    以6G内存规格的主机为例，假设其上运行有三台4G内存规格的虚拟机，内存份额分别为20480、20480、40960，那么其内存分配比例为1:1:2。当三个虚拟机内部均逐步加压，策略会根据三个虚拟机的份额按比例分配调整内存资源，最终三个虚拟机获得的内存量稳定为1.5G、1.5G、3G。

    内存份额只在各虚拟机竞争内存资源时发挥作用，如果没有竞争情况发生，有需求的虚拟机可以最大限度地获得内存资源。例如，如果虚拟机B和C没有内存压力且未达到预留值，虚拟机A内存需求压力增大后，可以从空闲内存、虚拟机B和C中获取内存资源，直到虚拟机A达到上限或空闲内存用尽且虚拟机B和C达到预留值。以上面的例子，当份额为40960的虚拟机没有内存压力（内存资源预留为1G），那么份额为20480的两个虚拟机理论上可以各获得最大2.5G的内存。

  - **内存资源预留**

    内存预留定义多个虚拟机竞争内存资源的时候分配的内存下限，能够确保虚拟机在实际使用过程中一定可使用的内存资源。

    预留的内存被会虚拟机独占。即，一旦内存被某个虚拟机预留，即使虚拟机实际内存使用量不超过预留量，其他虚拟机也无法抢占该虚拟机的空闲内存资源。

- 虚拟资源动态复用

  虚拟机空闲时，可自动根据可设置的条件将其部分内存、CPU等资源释放并归还到虚拟资源池，以供系统分配给其他虚拟机使用。用户可在Web界面上对动态资源进行监控。

- ![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/icon-nodd.gif) **说明：**

  单个主机CPU复用率越低，主机及虚拟机性能越好，建议发放业务及后期维护时，单个主机CPU复用率不超过4:1。

- 虚拟机统计

  支持虚拟机、磁盘资源使用情况统计报表。

#### 虚拟机资源动态调整

FusionCompute支持虚拟机资源动态调整，用户可以根据业务负载动态调整资源的使用情况。虚拟机资源调整包括：

- 离线/在线调整VCPU数目

  无论虚拟机处于离线（关机）或在线状态，用户都可以根据需要增加或者减少虚拟机的VCPU数目。通过离线/在线调整虚拟机VCPU数目，可以满足虚拟机上业务负载发生变化时对计算能力灵活调整的需求。

- 离线/在线调整内存大小

  无论虚拟机处于离线或在线状态，用户都可以根据需要增加或者减少虚拟机的内存容量。通过离线/在线调整内存大小，可以满足虚拟机上业务负载发生变化时对内存灵活调整的需求。

- 离线/在线添加/删除网卡

  虚拟机在线/离线状态下，用户可以挂载或卸载虚拟网卡，以满足业务对网卡数量的需求。

- 离线/在线挂载虚拟磁盘

  无论虚拟机处于离线或在线状态，用户都可以挂载虚拟磁盘，在不中断用户业务的情况下，增加虚拟机的存储容量，实现存储资源的灵活使用。

  ![img](https://gitee.com/hanstack/hanstack_image/raw/master/image/icon-note.gif) **说明：**

  虚拟机处于离线或在线状态，且虚拟机使用的磁盘是虚拟化存储时，用户可通过增加已有磁盘容量的方式进行虚拟机存储容量的扩容。

#### 分布式资源调度（DRS）和电源管理（DPM）

**DRS**

FusionSphere可以在虚拟机创建和运行的过程中感知全局物理资源的使用情况，并通过智能调度算法，确定适合虚拟机运行的最佳主机，并通过热迁移等手段将虚拟机运行在最佳主机之上，从而提升全局业务体验。

**定义**

动态资源调度DRS（Dynamic Resource Scheduler），指采用智能负载均衡调度算法，并结合动态电源管理功能，通过周期性检查同一集群资源内各个主机的负载情况，在不同的主机间迁移虚拟机，从而实现同一集群内不同主机间的负载均衡，并最大程度降低系统的功耗。

- 系统轻载时，将迁移部分虚拟机，并将其集中在部分物理主机，随即将空闲主机下电。
- 系统重载时，将启动部分物理主机，并将虚拟机均衡分布在各主机中，以保证资源的供应和用户的体验。
- 通过计划任务，可根据系统运行的情况，分时段采取不同的资源调度策略，以满足不同场景的用户需求。

**依赖关系**

虚拟机存在以下绑定关系时，动态资源调度对该虚拟机失效。

- 绑定主机
- 绑定图形处理器
- 绑定USB设备

**DPM**

DPM:动态电源管理（Dynamic Power Management），根据业务情况，智能的将部分主机上下电。

对应FusionCompute中的“电源管理自动化”：电源管理自动化功能会周期的检查集群中的服务器的资源使用情况，如果集群中资源利用率不足，则会将多余的主机下电节能，下电前会将将要下电主机上的虚拟机热迁移到其他主机；如果集群资源过度利用，则会将离线的主机商店，以增加集群资源，奸情主机的负荷。

#### 虚拟网卡

虚拟网卡均有自己的IP地址、MAC地址，从网络角度来看，虚拟网卡与物理网卡一致。FusionCompute支持智能网卡，实现多队列、虚拟交换、QoS、上行链路聚合功能，提升虚拟网卡的I/O性能。

#### 分布式虚拟交换机（DVS）

计算虚拟化驱动网络虚拟化的发展。传统数据中心，一台服务器运行一个操作系统，通过物理网线与交换机相连，由交换机实现不同的主机的交换、流量控制、安全控制等功能。在计算虚拟化后，一台服务器虚拟化成多台的虚拟的主机，每个虚拟主机有自己的CPU、内存和网卡。同一服务器上的不同主机之间既需要维持原有的通信，同时由于共享物理设备，引出了新的安全隔离、以及对流控的更高的需求，对虚拟交换技术的诉求由此产生。

为统一和简化对各台主机的虚拟交换机的配置管理，业界引入分布式虚拟交换机。分布式虚拟交换机一方面可以对多台服务器的虚拟交换机统一配置、管理和监控，另一方面也可以保证虚拟机在服务器之间迁移时网络配置的一致性。

简化和增强FusionSphere环境中的虚拟机网络连接，并提供统一管理入口、安全组等网络功能。

**定义**

分布式虚拟交换管理，即实现系统管理员对一至多台CNA服务器上的虚拟交换机的物理端口和虚拟端口进行配置/维护。

![image-20200829230900940](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829230900940.png)

| Firewall：防火墙  | Uplink Port：级联端口 | vNIC：虚拟机网络接口卡 |
| ----------------- | --------------------- | ---------------------- |
| VSP：虚拟交换端口 | -                     | -                      |

**分布式虚拟交换机模型的基本特征：**

1. 用户可以配置多个分布式交换机，每个分布式交换机可以覆盖集群中的多个CNA节点。
2. 每个分布式交换机具有多个分布式的虚拟端口VSP（Virtual Switch Port），每个VSP具有各自的属性（速率、统计和ACL等），为了管理方便采用端口组管理相同属性的一组端口，相同端口组的VLAN（Virtual Local Area Network）相同。
3. 每个分布式交换机可以配置一个Uplink端口组，用于虚拟机对外的通信，Uplink端口组可以包含多个物理网卡，这些物理网卡可以配置负载均衡策略。
4. 每个虚拟机可以具有多个vNIC（Virtual Network Interface Card）接口，vNIC可以和交换机的VSP一一对接。

**创建分布式虚拟交换机**

![image-20200829231401299](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829231401299.png)

**上行链路（Uplick）是什么**

上行链路是链接虚拟交换机（VSW）和物理机上网卡的通道；虚拟机交换机通过上行链路将虚拟机产生的流量转发到物理网络中。

**Linux Bridge介绍**

Linux Bridge（网桥）是工作于二层的虚拟网络设备，功能类似于物理的交换机。

Bridge可以绑定其他Linux网络设备作为从设备，并将这些设备虚拟化为端口，当一个从设备被绑定到Bridge上时，就相当于真实网络中的交换机端口插入了一个连接有终端的网线。

![image-20200829235610175](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200829235610175.png)

#### 虚拟机热迁移

当虚拟机在物理机上运行时，物理机可能存在资源分配不均，造成负载过重或过轻的情况。另外，物理机存在硬件更换、软件升级、组网调整、故障处理等操作，如何在不中断业务的情况下完成这些操作十分重要。虚拟机热迁移技术可以在业务连续前提下，完成负载均衡或上述操作，提升用户体验和工作效率。虚拟机热迁移通常是将整个虚拟机的运行状态完整保存下来，同时可以快速恢复到原有的甚至不同的硬件平台上。虚拟机恢复后，仍然可以平滑运行，用户感知不到任何差异。根据虚拟机数据存储在当前主机还是远端存储设备（共享存储）的不同，分为共享存储热迁移和非共享存储热迁移两种方式。

**迁移过程**

- 将虚拟机配置和设备信息传送到目标主机上

- 传送虚拟机内存（内存分片+迭代迁移）

  ​	将虚拟机迁移时的初始内存以及内存变更分片同步到目标主机上

- 暂时源虚拟机并传送状态

  ​	在源主机上暂停虚拟机

  ​	将最后的变更内存传送到目标主机上 

- 恢复目标虚拟机

  ​	在目标主机上恢复虚拟机，并在源主机上停止虚拟机

  ![image-20200830212523450](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200830212523450.png)

> 讨论：内存分片；迭代迁移
>
> 我们在这里讨论虚拟机为共享存储场景下虚拟机内存迁移的过程。
>
> - 虚拟机热迁移时业务是不中断的所以会有持续的数据IO，那么在执行虚拟机热迁移时为了保证源端和目的端数据的一致性会在执行迁移前对数据进行定格。定格后的数据是只读的，数据定格之后虚拟机业务产生的IO数据会被存放到新开辟的内存空间当中。
> - 将定格的数据从源端迁移到目标端，完成本次定格数据迁移之后。将虚拟机在此次迁移过程中写入到内存中的数据进行定格，然后迁移定格数据。新下发的IO数据写入到新开辟的内存空间当中。
> - 重复这个过程，随着迁移次数的增加。每次进行迁移的定格数据越来越少，迁移所花费的时间也越来越短。
> - 当需要进行迁移的数据达到一个阈值之后就暂时中断虚拟机的IO将最后的内存数据迁移到目标虚拟机上；此时源端和目标端的内存数据一致。将源端的业务IO流量迁移到目标端虚拟机上。完成迁移迭代。

**虚拟机迁移的类型**

| 迁移类型 | 子类           | 说明                                                         |
| -------- | -------------- | ------------------------------------------------------------ |
| 手动迁移 | 按目的迁移     | 系统维护人员通过FusionCompute的虚拟机迁移功能，手动迁移一台虚拟机到另一台服务器上。 |
| 自动迁移 | 虚拟机资源调度 | 在同一个集群内，系统根据预先设定的虚拟机调度策略，对虚拟机进行自动迁移。 |

**热迁移的应用场景**

- 当物理机故障或者负载过重时，可以将运行的虚拟机迁移到另一台物理机上，以避免业务中断，保证业务的正常运行。
- 当多数的物理机负载过轻时，可以将虚拟机迁移整合，以减少物理机数量，提高资源的利用率。
- 当物理服务器硬件设备成为瓶颈，比如CPU、内存、硬盘等，需要更换性能更好的硬件，或者需要增加设备，但是又不能关闭虚拟机或者停止业务。
- 服务器软件升级，比如虚拟化平台升级，就可以把虚拟机从旧版本虚拟化平台热迁移到新版本虚拟化平台。

**热迁移的注意事项和约束条件**

- 热迁移过程中，需要保证网络状态良好。如果发生网络中断，热迁移会暂停，直到网络恢复后才会继续，当发生超时，热迁移会失败。
- 迁移过程中，不允许对虚拟机进行生命周期和管理虚拟机硬件设备等操作。
- 虚拟机正在迁移的过程中，应尽可能保证源端、目的端服务器不被意外下电或重启，否则会导致热迁移失败，甚至可能导致虚拟机被下电。
- 虚拟机正在迁移的过程中，不允许对虚拟机做关机、重启或恢复操作，否则可能会导致热迁移失败，当执行ACPI方式重启时，再执行热迁移会导致虚拟机关闭。
- 只支持同构热迁移，即源端和目的端CPU型号需要相同。异构迁移需要开启IMC模式。
- 跨业务网段虚拟机迁移可以成功，但是到目的端后会出现网络异常，为了防止该情况发生，需要用户保证迁移业务网段一致。
- 如果源端虚拟机vCPU数大于目的端的物理机CPU核数，则迁移后将会影响到虚拟机的性能，应保证目的端物理机CPU核数大于等于源端虚拟机vCPU数。

**热迁移的前提条件**

- 进行热迁移之前要确保源端和目的端主机之间的网络是互通的，并且源端和目的获得资源权限是对等的，即两端同时能够访问到相同的存储资源和网络资源。
- 在执行虚拟机热迁移前应当对虚拟机进行健康检查，并确保目的端主机有足够的CPU、内存和存储资源。

**虚拟机热迁移时，传递的配置信息和设备信息具体是什么？保存在哪里？**

配置信息和设备信息统称为虚拟机的描述信息，其中配置信息指的是虚拟机的操作系统，引导方式，引导次序等；后者是虚拟机的CPU个数，内存大小，硬盘以及网卡的信息等。这些信息保存在**VRM**的数据库中，CNA上的**Libvirt**中还会以文件的形式存放在VM系统盘所在虚拟数据存储上。

**虚拟机热迁移的中断时长**

热迁移的中断时长在自由度比较高的产品上是可以进行设置的比如设置为500ms等

```
# virsh migrate-setmaxdowntime openEulerVM 500
```

在华为的产品上执行热迁移一般认为允许丢1到2个包。一般丢一个。

**虚拟机热迁移需要三层互通还是二层互通**

三层互通即可，跨数据中心的虚拟机热迁移需要保证两端的虚拟机关联同一个DVS。

**虚拟机形式部署的VRM能执行虚拟机迁移吗？**

不能；默认“与主机绑定”

#### 迁移VRM虚拟机

**操作场景**

当VRM虚拟机所在主机进行部件更换、重装系统等必须重启主机的操作时，需要将VRM虚拟机迁移到其他主机。

**前提条件**

- 目标主机上不存在其他VRM虚拟机。
- 目标主机和VRM虚拟机所在主机的CPU型号相同。
- 目标主机的虚拟化本地硬盘剩余空间大于140GB。
- 已获取VRM主节点和目标主机的“gandalf”和“root”用户的密码。
- VRM虚拟机运行正常。主备部署时，请确保主备VRM虚拟机均运行正常。

**单节点执行VRM虚拟机迁移**

- 将单节点部署的VRM扩展为主备部署
- 执行备节点的VRM虚拟机迁移

**主备节点执行VRM虚拟机迁移**

- 如果是执行主节点的迁移，需要将主被节点转换为备节点执行迁移
- 如果是被节点执行迁移，执行迁移操作即可

#### 虚拟机高可用性HA

虚拟机高可用性是当计算节点上的虚拟机出现故障时，系统自动将故障的虚拟机可以在几分钟内在正常运行的硬件上自动启动，使故障虚拟机快速恢复。

当系统检测到虚拟机故障时，系统将选择正常的计算节点，将故障虚拟机在正常的计算节点上重新创建。

- 计算节点掉电恢复或重启

  当计算节点掉电恢复或重启时，系统将计算节点上具有HA属性的虚拟机重新创建至其他计算节点。

- 虚拟机蓝屏（**X86**）

  当系统检测到虚拟机蓝屏故障且该虚拟机蓝屏处理策略配置为HA时，系统选择其他正常的计算节点重新创建虚拟机。（**注意：**这意味蓝屏是否执行HA是可选配置）

 **依赖关系**

集群中需要有足够的资源供虚拟机HA使用。

**VRM判断VM蓝屏或者内核崩溃**

检测VM蓝屏或者内核崩溃是根据tools和VRM之间维持的心跳关系。当一个虚拟化周期内VRM收不到心跳，则认为VM存在蓝屏或者崩溃，判断蓝屏或者崩溃的依据是VM是否有IO操作，如没有则认为内核崩溃，反之则认为蓝屏故障。

**Tools功能**

安装并启动Tools后，用户无需做任何操作，Tools即可提供以下功能：

- 为虚拟机提供高性能的磁盘I/O和网络I/O功能
- 为虚拟机提供虚拟硬件监控功能
  - 获取虚拟机指定网卡IP信息
  - 获取虚拟机内部各CPU利用率、内存利用率
  - 获取虚拟机内各个磁盘/分区的空间使用信息
- 为虚拟机提供高级功能
  - 在线调整虚拟机的CPU规格
  - 创建虚拟机快照
  - （**X86架构**）虚拟机蓝屏检测
  - 虚拟机与主机时钟同步
  - 虚拟机网卡的高级功能，如QoS
  - 自动升级虚拟机的驱动程序，如Tools驱动

#### DPDK（用户态交换模式）（x86）

通过使用DPDK（Data Plane Development Kit，数据平面开发套件，DPDK是一系列库和驱动的集合）技术，用来在X86平台进行快速的数据包处理。它通过环境抽象层旁路内核协议栈、轮询模式的报文无中断收发、优化内存/缓冲区/队列管理、基于网卡多队列和流识别的负载均衡等多项技术，实现了在X86处理器架构下的高性能报文转发能力，提高虚拟机网络性能。

#### SR-IOV

SR-IOV实现了将PCI功能分配到多个虚拟接口以在虚拟化环境中共享一个PCI设备的资源。SR-IOV能够让网络传输绕过软件模拟层，直接分配到虚拟机。这样就降低了软件模拟层中的I/O开销。

**ARM架构X86架构支持特性对照**

| 关键特性             | x86                   | ARM    |
| -------------------- | --------------------- | ------ |
| 跨主机热迁移         | 支持                  | 支持   |
| 内存复用             | 支持                  | 不支持 |
| 虚拟机高可靠性HA     | 支持                  | 支持   |
| 虚拟机存储热迁移     | 支持                  | 支持   |
| 自动精简配置         | 支持                  | 支持   |
| 虚拟化防病毒         | 支持                  | 不支持 |
| 容灾备份             | 支持                  | 支持   |
| 动态资源调度DRS      | 支持                  | 支持   |
| 动态电源管理DPM      | 支持                  | 支持   |
| 虚拟机资源QoS        | 支持                  | 支持   |
| 用户态交换模式(DPDK) | 支持（**Intel技术**） | 不支持 |
| 分布式虚拟交换机DVS  | 支持                  | 支持   |
| SR-IOV               | 支持（**Intel技术**） | 不支持 |
| GPU直通              | 支持                  | 支持   |
| GPU虚拟化            | 支持                  | 不支持 |



## 09-两台虚拟机不通

在考察两台虚拟机之间不同的问题一般会由自身到外部考虑。

**虚拟机本身的问题**

- 虚拟机防火墙是否关闭
- 两个虚拟机IP（IP配置，网关，跨网段）
- 两个虚拟机所链接的端口组是否有问题

**根据流量走向分析**

![image-20200831085250964](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831085250964.png)

- 同主机；同vlan：
- 同主机；不同vlan：
- 不同主机；不同vlan
- 不同主机；相同vlan

**其他问题**

- 物理线路问题
- 外部网络设备（交换机；路由器）故障
- 物理交换机上没有放行相应的VLAN

 **什么是DVS由什么组成**

![image-20200831153836332](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831153836332.png)

DVS是分布式虚拟交换机，是由VRM维持并跨越多个物理节点（CNA）的逻辑交换机。实际上在上图中横跨两个节点的DVS是由两个节点内部的虚拟交换机构成的。DVS有点类似于物理交换机的堆叠，可以理解成为通过两个EVS之间的连接线将两个EVS做了堆叠。

**什么是上行链路**

上行链路是在物理交换机上指的是物理交换机进行级联的端口；在虚拟交换机中指的是虚拟机交换机连接物理网络的通道；实际上我更偏向与是多台虚拟机交换机进行级联的通道。也就是虚拟交换机进行堆叠。

**什么是端口组**

端口组是一组具有相同属性端口的集合。通过端口组对端口组内的端口统一配置端口策略。虚拟机的虚拟网卡连接到分布式交换机的端口组，这样，即使与同一端口组相连接的虚拟机各自在不同的物理服务器上，这些虚拟机也都属于虚拟环境内的同一网络。

连接在同一端口组的虚拟机网卡，具有相同的网络属性（如：VLAN、QoS、安全属性等），以提供增强的网络安全、网络分段、更佳的性能、高可用性以及流量管理。

**端口类型**

普通类型的虚端口只能属于一个VLAN，中继类型的虚端口可以允许多个VLAN接收和发送报文。

普通虚拟机选择普通类型的端口；因为普通虚拟机发送的数据是不带vlan标签的数据。

虚拟机网卡启用vlan设备选择中继（trunk）端口，否则虚拟机网络不通。实际上所谓的中继端口就是trunk类型的端口。

**端口组属性**

![image-20200831162350686](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831162350686.png)

- DHCP隔离：使用该端口组的虚拟机无法启动DHCP Server服务，以防止用户无意识或恶意启动DHCP Server服务，影响其他虚拟机IP地址的正常分配。（**安全组虚拟机、“SRIOV直通模式”不支持配置该项。**）

- IP与MAC地址绑定：仅当选择“端口类型”为“**普通**”，且虚拟机为通过自定义虚拟机方式设置的IP时有效。使用该端口组的虚拟机，其IP地址与MAC地址绑定，防止用户通过修改虚拟机网卡IP地址或者MAC地址，发起IP或者MAC仿冒攻击，增强用户虚拟机的网络安全性。开启该功能时，如果某个虚拟机网卡配置多个IP地址会导致该网卡部分IP地址通信异常，建议在虚拟机网卡配置多个IP地址时不开启该功能。（**“SRIOV直通模式”、“用户态驱动模式”不支持配置该项。**）

- 填充TCP校验和：该端口组下的虚拟机在接收报文时，系统会自动填充TCP校验和。此开关只应用于对TCP校验和的正确性有要求的场景，不建议开启。开启开关后虚拟机网络收包性能会有所下降。（**安全组虚拟机、“SRIOV直通模式”、“用户态交换模式（DPDK）”不支持配置该项。**）

- 发送流量整形：

  1.发送平均带宽（Mbit/s）：某段时间内允许通过端口的平均每秒发送位数。

  使用普通网卡时，在没有突发大小可使用的情况下，流量将稳定在平均带宽所设定的速率上。如果突发大小设置过小，网络带宽吞吐量会变差。

  2.发送峰值带宽（Mbit/s）：发送流量突发时，每秒钟允许通过端口的最大传输位数。

  峰值带宽需要大于或等于平均带宽。对某一类流量设置合适的峰值带宽，可以防止因为该类流量过大导致其他虚拟机网络拥塞。带宽空闲时，流量将稳定在平均带宽左右。

  3.发送突发大小（Mbits）：允许流量在平均带宽的基础上产生的突发流量的大小。

  4.优先级：在物理网口发生网络拥塞的时候，各系统接口或VSP的流量会依据所设置的优先级参数去抢占物理网口的带宽，优先级高的优先使用网络宽带资源。

  ​	低

  ​	中

  ​	高

  不同场景下带宽的浮动范围有所不同。当物理网口的转发能力大于服务器内各系统接口和VSP的平均带宽之和并小于峰值之和时，各虚拟机流量将在平均带宽之上，不超过峰值带宽。当物理网口的转发能力小于服务器内各系统接口和VSP的平均带宽之和时，各虚拟机的流量最小将有可能小于平均带宽，最大不会超过峰值带宽。（如果待添加端口组的分布式交换机的交换类型为“用户态交换模式”，则不支持设置优先级。“SRIOV直通模式”只支持“发送平均带宽（Mbit/s）”。）

**安全组**

![image-20200831164122975](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831164122975.png)

位于同一个安全组的所有虚拟机网卡都将使用该安全组规则进行网络通信。虚拟机一块网卡只能加入一个安全组中。

安全组是一种虚拟的防火墙，使用iptable进行数据包过滤功能，用于设置云服务器，负载均衡，云数据库等实例的网络访问控制，控制实例级别的出入流量。

![image-20200831164403621](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831164403621.png)

**OVS、EVS、DVS之间区别和联系**

- OVS

Open vSwitch（OVS）是一款基于软件实现的开源虚拟以太网交换机。

OVS能够支持多种标准的管理接口和协议，还可以支持跨多个物理服务器的分布式环境。

OVS提供了对OpenFlow协议的支持，并且能够与众多开源的虚拟化平台相整合。

主要有两个作用：传递虚拟机VM之间的流量，以及实现VM和外界网络的通信。

![image-20200831164654846](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831164654846.png)

OpenFlow交换机将原来完全由交换机/路由器控制的报文转发过程转化为由OpenFlow交换机（OpenFlow Switch）和控制服务器（Controller）来共同完成，从而实现了数据转发和路由控制的分离。控制器可以通过事先规定好的接口操作来控制OpenFlow交换机中的流表，从而达到控制数据转发的目的。

OpenFlow网络由OpenFlow交换机、FlowVisor和Controller三部分组成。OpenFlow交换机进行数据层的转发；FlowVisor对网络进行虚拟化；Controller对网络进行集中控制，实现控制层的功能。

OpenFlow交换机由流表、安全通道和OpenFlow协议三部分组成。

- EVS

  - Elastic Virtual Switch（EVS）是基于OVS转发技术，提升了其IO性能的一种弹性化虚拟交换。

    仍然符合openflow协议标准。

    其中IO性能提升使用了Intel DPDK技术，通过用户态进程接管网卡数据收发，采用“IO独占核”技术，即每个端口分配一个核专门用于数据收发，这种轮询式的处理方式比中断式的处理更高效，因而IO性能方面有显著提升。

    EVS关键技术：

    物理网卡访问：DPDK高速数据通道

    报文处理：使用大页内存

    交换业务处理

    轮询转发，减少调度开销

    多核（线程）并行处理

    Openflow流表转发优化

    前后端：vhost-user技术

![image-20200831165727957](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200831165727957.png)

- DVS

  DVS是分布式虚拟交换机，是由VRM维持并跨越多个物理节点（CNA）的逻辑交换机。实际上在上图中横跨两个节点的DVS是由两个节点内部的虚拟交换机构成的。DVS有点类似于物理交换机的堆叠，可以理解成为通过两个EVS之间的连接线将两个EVS做了堆叠。

## 10-FusionCompute磁盘技术

### 存储热迁移：

热迁移可以在不中断业务的前提下，将虚拟机磁盘从一个存储迁移至另一个存储。热迁移的技术原理如下：

![image-20200909015210493](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909015210493.png)

- 热迁移首先使用写时重定向，将虚拟机数据写入目的存储的一个差异磁盘，这样，原磁盘文件就变成只读的。

- 将源卷的所有的数据块依次读取出来并合并到目标端的差异磁盘中，等数据合并完成后，目的端的差分磁盘就拥有虚拟磁盘的所有最新数据。

- 去除目的端快照对源卷的依赖，将差分磁盘修改为动态磁盘，这样，目的端磁盘文件可以独立运行。

### FusionCompute中存储的基本概念：

- 存储资源：

  存储资源表示**物理存储设备**，例如IP-SAN、FC -SAN、NAS等。
- 存储设备：

  存储设备表示**存储资源中的管理单元**，类似LUN、 FusionStorage存储池、NAS共享目录等。

  一个存储资源可以有多个存储设备
- 数据存储：

  数据存储表示虚拟化平台中可管理、操作的存储逻辑单元。

  一个数据存储和一个存储设备相对应

  数据存储承载了具体的虚拟机业务，例如创建磁盘等。

### FusionCompute磁盘文件

- 普通磁盘：

  普通磁盘创建时大小与虚拟磁盘大小相同，并将文件所有位置填0，占用空间较大，置备时间较长。

  该磁盘用于FusionSphere系统中的普通磁盘。普通磁盘使用普通磁盘技术，创建时会进行全空间分配和置零操作，能够提供最好的性能体验和数据安全性。适用于对IOPS要求较高的场景。该磁盘创建所需时间会比创建其他类型的磁盘长。

  ![image-20200909020613479](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909020613479.png)

- 普通延时置零磁盘：

  普通延迟置零卷创建时大小与虚拟磁盘大小相同，但不会进行填0操作，占用空间较大，置备时间较普通卷短。

  该磁盘用于FusionSphere系统中的普通延迟置零磁盘，可以提高存储设备的利用率。普通延迟置零磁盘创建很快，创建时进行全空间分配，但未进行全置零动作，性能较普通磁盘有所下降。适用于对发放速度要求高，但对IOPS要求不高的场景

  ![image-20200909020536879](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909020536879.png)

- 精简磁盘：

  精简磁盘创建时大小为0，精简磁盘创建时含少量元数据信息，大小一般为几十K，创建时间均非常短。随着用户写入数据，精简磁盘的大小与实际占用空间将逐步增加。

  该磁盘用于FusionSphere系统中的精简磁盘，可以提高存储设备的利用率。精简磁盘使用动态磁盘技术，可以节省存储空间。该磁盘在创建时不进行空间分配，而是在用户IO写入磁盘文件时才进行空间动态分配，性能较普通磁盘有所下降。适用于用户对存储需求不明确，或是规划的容量比实际使用的容量多的场景。

  ![image-20200909020440175](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909020440175.png)

- 持久化磁盘：

  持久化磁盘即数据可以永久保存。在创建独立持久磁盘时，快照中不包含该磁盘，更改将立即并永久写入磁盘，回滚快照不会导致数据回滚。类似于U盘，应用于个人独有数据存放。
- 非持久化磁盘：

  非持久化磁盘即数据不永久保存。处于保护磁盘数据的目的，在启动虚拟机时，对这种非持久化磁盘先创建差分磁盘，在虚拟机运行过程中，将有更改的数据全部写入差分磁盘，在虚拟机关机后，将差分磁盘数据删除，达到还原磁盘的目的。应用于公共计算机、计算机数据自动还原的场景。

### 虚拟磁盘文件

- 固态磁盘文件：

  存储块空间全部进行初始化成“0”

  IO性能最佳，用于对IOPS要求较高的场景

  磁盘大小恒定，创建后使用空间和预留空间相等
- 动态磁盘文件

  创建时只需写头和结束块，创建速度快

  IO性能较差，应用于精简磁盘和普通延迟置零磁盘

  磁盘大小会随着用户写入数据而增长，但不随着用户删除数据而缩减
- 查分磁盘文件

  差分磁盘的结构和动态磁盘一模一样，只是文件头中会记录它的父文件路径

  配合快照、非持久化磁盘、链接克隆等功能被使用，起到保护源盘不被修改，并可以跟踪虚拟机磁盘差异数据的作用

  差分卷不能独立存在，必须能够访问到父文件才能正常工作

### 存储资源裸设备映射

RDM为虚拟机提供了一种机制来直接访问物理存储子系统（仅限光纤通道或iSCSI）上的LUN，通过使用物理设备映射，可以让虚拟机识别SCSI磁盘

![image-20200909021221772](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909021221772.png)

- 不支持链接克隆、存储瘦分配、磁盘在线/离线扩容 、存储增量快照 、iCache 、存储热迁移、存储QoS、磁盘备份、虚拟机转为模板等
- 技术特点
  - 虚拟机直接通过SCSI命令操作裸存储设备
  - 兼容FC光纤存储和IP SAN存储
  - 适用于需要高性能存储的应用，比如Oracle RAC

### 存储扩容

![image-20200909021457118](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200909021457118.png)

- FusionCompute提供了存储扩容的类型有虚拟卷扩容和数据存储扩容。

- FusionCompute支持在离线或在线状态下对磁盘的容量扩充，对于普通磁盘，会将数据区域进行扩充，并进行写零。对于普通延时置零磁盘，会将数据区域进行扩容，并进行空间预占。对于精简磁盘，仅对数据区域进行扩容。

- 数据存储扩容使得一个数据存储可以管理多个物理LUN空间，当需要扩容数据存储时，可以通过添加另外的物理LUN至数据存储或者对物理LUN进行扩容再扩容数据存储，从而实现对数据存储灵活地进行空间扩容，有效提高数据存储扩展性
- -当需要扩容时，先在主节点上将新增的存储空间以线性映射的方式追加至虚拟块设备末尾，完成虚拟块设备的扩容后，再将新增的存储空间分成数段逐渐增加至文件系统 (更新文件系统中的元数据)，主节点完成数据存储的扩容。由于虚拟块设备的信息都是保存在节点内存中，则当其它节点发现数据存储空间有变化时，则需要更新虚拟块设备信息，完成扩容虚拟块设备。

