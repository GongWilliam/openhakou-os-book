---
title: 4.关于 CPU 你需要了解的
tags: 
- 操作系统
categories:
- 操作系统
---

# 4.关于 CPU 你需要了解的
我们如果留意的话，可能经常会听到什么 **x86/x86_64、ARM、RISC-V** 等关键词，但是很多人并不知道指的是什么。比如：Java 程序员在下载官方 JDK 时，如果对这个概念不了解的话，只知道选 x64 那个东西下载就好，其他一脸懵逼。所以有必要了解一下 CPU 的相关知识。

这篇主要讲解一下三个知识点：

- CPU 内部结构
- CPU 指令集
- SMP 和 NUMA

## 1.CPU 内部结构

CPU 内部有很多组件，比如 **MMU/TLB** 等，但是主要的部件主要有三类：**控制单元、存储单元、运算单元**。

![img](https://i.loli.net/2021/03/12/t4Ew9JzgxdDAYuB.jpg)

**控制单元由程序计数器（PC）、指令寄存器（IR）、指令译码器（ID）等控制器组成**，很多同学应该对程序计数器（PC）比较耳熟。控制单元主要功能有：

> - 从内存中获取指令
> - 对指令进行译码，译码得到电气信号用来控制 CPU 的操作
> - 控制 CPU 与内存的数据流动
>

**运算单元主要部件是算术逻辑单元（ALU）**。控制单元会将对应的数据输入到这个部件，让它进行数据的运算。它能支持的运算主要是加减乘除、移位、逻辑。

**存储单元它主要用来暂时存放 cpu 将要获取的数据或者刚产生的数据，它由专用寄存器和通用寄存器两个部分组成**。通用寄存器是可以由程序员进行控制的，一般可用汇编语言来控制。那专用寄存器和通用寄存器都有哪些呢？这个问题就比较专业了，我也记不住那些 AX、BX、CX、DX、BP、SP、SI、DI 的名称。我们大概了解一下它里面的组成，想了解的可以去翻一下《深入理解计算机系统》第三版，第二版讲的是 32 位的，第三版的是 64 位。现在市面上 CPU 基本都是 64 位的了，所以最好还是看第三版。

## 2.CPU 指令集

![img](https://i.loli.net/2021/03/12/pSoXQEhGkqtJjYO.jpg)

**什么是指令集？它有什么用？**

**指令集是指挥 CPU 工作的指示和命令**，比如：让某个数进行乘法运算。指令集有很多种，因为在早期每个芯片制造公司都各自为政搞自己的 CPU 指令集，比如 Intel，IBM，AMD 等。不同的指令集对于表达同一个事的效率是不一样的，有些比较高效，有些比较低效冗余。比如：让 100 乘以 2，高效的指令集会让 100 进行往左移一位（左移一位相当于乘以2），低效的指令集则会让 ALU 进行 100 x 2 乘法运算。

指令集虽然有很多种，但是可以划分为两大类：**(RISC) 精简指令集 和 (CISC) 复杂指令集**。指令集刚出来的时候，芯片设计公司都在往指令集里面不断的加指令，显得功能很强大，所以按照时间线来说 CISC（复杂指令集） 出来得比较早。后来有个工程师发现大部分指令其实用不到，这里面涉及到了一个 "80/20 原则"：CPU 百分之八十的功能只会用到指令集中百分之二十的指令。所以这个工程师将大部分的指令删除了，精简了指令集，这就成为了 RISC（精简指令集）。

目前市场上主要有如下这些指令集：

> **x86/x86_64**：属于 CISC 复杂指令集。这两个指令集是 Intel 和 AMD 公司分别开发出来的，这里面边有个小故事，有兴趣的同学可以去搜索一下。现在台式机 CPU 和服务器 CPU 大多使用的是这个类型的指令集。
>
> **ARM**：属于 RISC 精简指令集。大名鼎鼎的 ARM 公司开发的产品，这个公司只进行芯片设计，不涉及制造。我们用的手机或者物联网设备上的芯片大多是这个类型的指令集。为什么会这样呢？因为这个 ARM 类型的芯片功耗较低。
>
> **MIPS**：属于 RISC 精简指令集。目前市面上不常见这种指令集的 CPU，但是我国的龙芯采用的是这种指令集，听说它的设计是很精致的，但由于这种指令集的约束力不强，可以自由发挥，导致现在市场越来越少这种指令集。最近听说 MIPS 母公司叛逃 RISC-V 了。
>
> **RISC-V**：属于 RISC 精简指令集。冉冉升起的新星，它是在美国大学里面的实验室诞生并开源，目前还没成熟，但是听说很有看头。

关于 CISC 和 RISC 哪个更好现在没有定论，只是学术界大多比较倾向于 RISC，而老牌的芯片厂商则由于历史包袱一直对已有的 CISC 改良革新，孰强孰弱让历史见证吧。

## 3.SMP 和 NUMA

很多同学可能对这个并不了解，但在基础研发的同学看来这是必知必会的知识点。

芯片里面每个核心如何存取数据是有不同的设计的，比较常见的设计有 **SMP 和 NUMA**。

**SMP：对称多处理器结构(Symmetric Multi-Processor)**。假设一个 CPU 里面有 4 个核，每个核访问任何一个内存地址所用的时间都是一样的。如下图所示：

![img](https://i.loli.net/2021/03/12/vbfBs7HkUiVuleL.png)

**NUMA：非一致存储访问结构(Non-Uniform Memory Access)**。假设一个 CPU 里面有 4 个核，每个核访问任何一个内存地址所用的时间都不太一样。如下图所示：

![img](https://i.loli.net/2021/03/12/uZKhpRcOYSvVBIg.png)

SMP 和 NUMA 适用的场景不同，**SMP 适用于个人 PC**，也就是我们的个人用的电脑 CPU 大多是 SMP 架构，**因为 SMP 架构在 2-4 个核的时候性能是最好的**，而个人用的计算机基本上 2-4 核已经够用了。**NUMA 适用于服务器领域**，服务器是很多核心的，一般都 100 个核心往上走。在服务器这种场景中，如果用 SMP 架构的话性能会很慢，你想想 100 个人同时冲向一个食堂打饭，场面肯定混乱不堪，但是把食堂分成 4 个小地方开设，让 100 个人就近去 4 个食堂打饭，那肯定比去同一个食堂打饭强很多。

SMP 架构和 NUMA 架构使用的场景不同，就像排序算法中的冒泡排序和快速排序，在小数据量的情况下其实冒泡也是很快的。



欢迎关注公众号：哈扣。

![](https://i.loli.net/2021/03/12/Tt3uBvRqDQarMI2.jpg)

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/3.0/cn/80x15.png" /></a> 本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/">知识共享署名-非商业性使用-相同方式共享 3.0 中国大陆许可协议</a>进行许可。