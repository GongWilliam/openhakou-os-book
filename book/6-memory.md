---
title: 6.关于内存你需要了解的
tags: 
- 操作系统
categories:
- 操作系统
---

# 6.关于内存你需要了解的

关于内存可能大家都知道它是计算机中不可或缺的一部分，也知道**程序是跑在内存中**的。如果关于内存知识只知道这两点的话，我想这篇文章应该是给你准备的。这篇主要讲的内容包括以下三个内容：

- 内存以及它的特性

- 关于 DDR 与 DIMM

- 关于双通道


## 1.内存以及它的特性

![img](https://i.loli.net/2021/03/12/XKzc7f9mTiM8PVy.jpg)

内存在英文里表达为 Memory。实际上在计算机里面很多与存储相关的东西都叫 Memory，比如上一期的 CPU 缓存。

那内存为什么叫内存呢？内存在计算机中属于存储器的一种，**存储器分为主存储器（Primary Memory）和辅存储器(Secondary Memory)**。**主存储器指的是能够被 CPU 直接寻址的存储部件，目前能够做到这点的存储器是我们熟悉的内存条**。能够被 CPU 直接寻址意味着 CPU  能够对它直接发送指令，让内存条把对应位置的存储信息进行存或取，而辅存储器是不能被 CPU 直接寻址的。

我们经常会看到 RAM 这个词，而且通常会用这个词来指代内存。RAM（Random Access Memory）指的是随机访问存储器，它可以读也可以写，但是掉电后信息会丢失。目前 RAM 可以分为两类：

> **DRAM(Dynamic Random Access Memory)：动态随机访问存储器**。它的结构简单，单位存储价格低，适合做主存，但是它的速度比下面的这个 SRAM 慢。
>
> **SRAM(Static Random Access Memory)：静态随机访问存储器**。它的结构复杂，单位存储价格高，速度比较快，所以一般将它用来做上一节讲到的 CPU 缓存。

内存条的物理特性如下：

> **随机存取**。访问内存上的任何一个位置的信息所需要的时间是一样的。
>
> **易失性**。当内存条掉电时上面所有的信息会丢失，所以如果需要保存上面的信息，那就得把内存的数据保存到辅存储器上，比如硬盘等。

**内存条有一个”刷新频率“的概念**，目前内存条常见的频率为 2133 MHz/ 2666 MHz / 3200 MHz，它指的是内存条每秒需要刷新的次数。为什么内存条需要刷新呢？内存条保存数据靠的是上面组件的电容特性，而电容是会放电的，当电容放电后数据就丢失了，所以为了维持住内存条上面的数据不丢失，必须得给这些电容充电，所以就有了这个刷新频率。**每刷新一次内存即意味着给上面的电容充电一次，维持住目前的数据不丢失**。

## 2.关于DDR 与 DIMM

![img](https://i.loli.net/2021/03/12/ln4mjSvL5HUW2wb.jpg)

当我们买内存条时，应该经常会听到什么 DDR3、DDR4、DDR5 等这些关键词，那这些词汇究竟指的是什么呢？

**DDR 的全称是 Double Data Rate SDRAM，简称双倍数据速率的 SDRAM**。那 SDRAM 又是什么鬼？上面我们讲到 RAM 分为了 DRAM 和 SRAM，这里的 SDRAM 属于 DRAM 的一种，它的全称是 Synchronous DRAM，中文译为同步动态随机访问存储器。说人话！就是它工作需要进行时钟同步。为什么要时钟同步？因为它要精确的控制它每秒的刷新频率呀，没有时钟它咋知道时间过了多久。

SDRAM 是很早期的内存条形态，它的金手指只有一面，而 DDR 的金手指是两面，这就意味着 DDR  的传输速率是 SDRAM 的两倍。说到金手指，这个**内存条的硬件接口也分为两种：SIMM 和 DIMM**。**SIMM 全称为 single in-line memory module，译为单边接触内存模组**，其实也就是说金手指只有一面，目前已经基本上淘汰了这种类型的内存条接口。**DIMM 全称Dual-Inline-Memory-Modules，译为双列直插式存储模块**，也就是说金手指是有两面的，目前最常见的接口类型。

比较耳熟的 DDR3 、DDR4、DDR5 分别是 DDR  的进化版本，性能依次增强。目前比较常见的是 DDR4，DDR5 的标准规范刚出来，厂商应该也快推出这个类型的内存条了。

## 3.关于双通道

![img](https://i.loli.net/2021/03/12/HZy53BXpkOiasj2.jpg)

可能很多人都知道**双通道的性能更好**，它指的是把两根规格一样的内存条安装到主板上，注意，是两根内存条，而且规格（频率、大小）是一样的。

**双通道内存技术的诞生是为了满足 CPU 带宽而研发的**，因为在单通道模式下很容易就到达了系统性能瓶颈，而**双通道技术则把内存的带宽提高一倍**，提高了这个瓶颈。这项技术的核心是依赖于内存控制器，这个控制器一般位于主板上。

现在的主板不仅可以双通道，有些还可以三通道。



欢迎关注公众号：哈扣。

![](https://i.loli.net/2021/03/12/Tt3uBvRqDQarMI2.jpg)

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/3.0/cn/80x15.png" /></a> 本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/">知识共享署名-非商业性使用-相同方式共享 3.0 中国大陆许可协议</a>进行许可。