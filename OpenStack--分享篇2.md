---
title: "OpenStack  分享篇2"
date: 2020-04-30T02:19:48+08:00
draft: true
---

## OpenStack分享篇02

## 虚拟化原理



```cpp
虚拟化的目的是逻辑表示自愿，摆脱物理限制的束缚。（用软件定义或者用软件的方式来实现的计算机称之为虚拟化；同样的我们可以实现虚拟化的存储，虚拟化的网络等）
实现的方式：在系统中添加一个虚拟化层，虚拟化层将下层资源进行封装，抽象成另一种形式的资源，提供给上层使用。如何实现CPU的虚拟化，换言之，如何将一个CPU虚拟成多个virtual CPU提供给我们创建的虚拟机使用呢？你在使用vCPU的时候到底使用的是什么？其实有很多的方式都能完成CPU的虚拟化，比如我们可以采用分时（Time shareing）的方式，将CPU的供支配的时间分给不同的VM进行使用。具体来说就是讲时间进行切片，例如将时间片分为1ms，那么每个程序或者业务都独享1ms的CPU使用时间，如果此时有两个程序或者说是我们这里的虚拟机，那么每个虚拟机都轮流的使用1ms的CPU，在微观上程序的运行是走走停停，在宏观上程勋的运行是连贯的。在具体的使用方式上又有时间片轮转调度算法，先来先服务算法等，在此不再赘述。
```

### 虚拟机（Virtual Machine）



```undefined
由虚拟化层提供高效，独立的计算机系统
拥有自己的虚拟硬件（CPU 内存 网络设备 存储设备）
对于上层软件，虚拟机就是真实的机器（运行在VM上应用和服务是无感知的）
VMM（Virtual Machine Monitor）：虚拟监视器
以虚拟监视器VMM或者hypervison作为分界线；我们笼统的将虚拟化分为上下两层。上层是Guest，使用的是VMM提供的各种虚拟化的资源构建的虚拟机。下层则是Host，Host提供VMM所需的各种物理资源以便VMM将其虚拟化，并且提供给上层的Guest使用。
```

![11](https://gitee.com/hanstack/hanstack_image/raw/master/image/11.png)



### VM的特征



```dart
同质：量可以不同，质与物理机本质相同，例如CPU的ISA（指令集架构Instruction Set Architecture）
高效：性能与物理机大致相当，大多数指令在硬件直接执行，只有少量由VMM模拟处理
资源可控，VMM对物理机，虚拟机的资源绝对可控
```

### 虚拟化的原理

#### 全虚拟化（Full Virtualization）



```undefined
抽象的VM具有完全的物理机的特质，OS在其上运行不需要任何的修改。典型的有VMware，VirtualBox，KVM-x86
```

#### 半虚拟化（Paravirtualization）



```undefined
需要OS协助的虚拟化，在骑上运行OS需要修改。起初采用是为了解决x86体系结构上完全虚拟化的困难（没有Intel VT&AMD-V硬件辅助虚拟化的支持；且不屑于动态扫描指令修补的方法性能。）后来则是主要为了提高虚拟化的效率。典型的有Xen，KVM-PowerPC等。
```

### CPU架构（x86）

![image-20200430002433824](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200430002433824.png)

image-20200430002433824.png



```undefined
CPU一般会分为用户态和核心态。（不同架构的CPU的指令等级是不同的）
X86的状态更多分为Ring 0 Ring1 Ring2 Ring3
CPU为啥要分为这么多状态呢？
一般来说是为了代码执行的安全，在早期的操作系统上，由于软件工程师的水平参差不齐，可能一个软件能够导致整个系统的崩溃，这主要是由于代码之间的隔离不够好，一个人写的程序代码可能会影响另一个人。甚至影响核心代码导致系统奔溃。
制造CPU的硬件厂商将指令分为用户态和核心态。一些硬件需要在处在核心态的操作系统才能够执行，而普通的软件处在用户态。应用软件如果想要通过API调用核心态的指令会被处于核心态的操作系统捕获，从而产生异常出错。
```

### 特权解除与陷入模拟：虚拟化的关键

![image-20200430004152955](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200430004152955.png)



```undefined
将GuestOS运行在非特权级别（特权解除），而将VMM运行在最高特权级别（完全控制）。 
解除了Guest OS的特权后，Guest OS的大部分指令仍可以在硬件上直接运行，只有当执行到特权指令时，才会陷入到VMM模拟执行（陷入-模拟）。
在上图中Host OS运行在Ring0当中，能够直接操作硬件。VMM运行在Ring3级别，无法直接操作硬件。Guest OS作为VM上运行的操作系统，显然是需要驱动硬件的，VM模拟出来的硬件也是硬件。但是现实GuestOS并不在Ring0级别，可能处于Ring1-Ring3之间。那么Guest OS应该如何驱动硬件呢？
在这里我么讨论两种情况，一种是全虚拟化，一种是半虚拟化。
全虚拟化的情况：
    由于Guest OS处在非核心态，不能使用Ring0指令，那么可以使用非核心态的指令去模拟核心的指令（用非核心态的指令去代替核心态的指令）当GuestOS发出Ring0级别的指令时，VMM捕获这些指令执行翻译，将其转换为非特权指令进行执行。此时VMM需要实时监控GuestOS并且进行实时的指令翻译，会对VMM带来较大的开销。VMM进行Ring0指令的动态翻译，由于有了这个翻译的过程，这种方式执行起来会比较慢。这种方式称之为（陷入模拟）
半虚拟化的情况：基
    修改GusetSO的操作系统代码，将核心态指令使用非核心态的指令进行改写或者替换。这种需要修改操作系统。称之为特权解除。
```

## 主要的虚拟化流派

### 基于二进制翻译的全虚拟化



![image-20200430010052739](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200430010052739.png)



```undefined
其主要思想是在执行VM上执行GuestOS指令时，翻译成X86ISA的一个子集，其中的敏感指令被替换成陷入指令。（敏感指令由VMM捕获翻译成非特权指令进行执行）
翻译过程和指令执行交叉进行
不含敏感指令的用户态程序可以不经翻译直接执行
该技术为VMware WorkStation,VMware ESX Server早期版本，Virtual PC以及QEMU（快速模拟器）采用。
```

### 基于扫描修补的全虚拟化



```undefined
VMM会在VM运行每块指令之前对齐进行扫描，查找敏感指令
补丁指令块会在VMM中动态生成，通常每一个需要修补的扫描指令会对应一块补丁指令
敏感指令被替换成一个外跳块，从VM跳转到VMM，在VMM中执行动态生成的补丁指令块
当补丁指令快执行完毕之后，执行流再跳转回VM的下一条指令处继续执行
SUN的VirtualBox采用该技术
```

### OS协助的虚拟化





![image-20200430011553260](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200430011553260.png)

```undefined
通过修改Guest OS的代码，将带有敏感指令的操作，替换为对VMM的超调用（Hypercall，类似于OS的系统调用，可将控制权转移到VMM）
该技术的优势在于VM的性能接近于物理机，缺点在于需要修改Guest OS
该技术因Xen广为人知
```

### 硬件协助的虚拟化





![image-20200430011712401](https://gitee.com/hanstack/hanstack_image/raw/master/image/image-20200430011712401.png)

```undefined
引入新的处理器运行模式和新的指令集，使得VMM和GuestOS运行于不同的模式下，Guest OS运行于受控模式，原来的一些敏感指令在受控模式下全部会陷入VMM，这样就解决了部分非特权敏感紫菱的陷入--模拟难题，而且切换时上下文的保存恢复全由硬件来完成，这样就大大提高了陷入-模拟时的上下文切换的效率。
该技术的引入使得X86可以很容易的实现完全虚拟化。目前已被广泛采纳。
```

##### 备注：OpenStack cloud computing cookbook




