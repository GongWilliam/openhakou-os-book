---
title: 10.二进制文件是什么样的？
tags: 
- 操作系统
categories:
- 操作系统
---

# 10.二进制文件是什么样的？

在讲述进程之前，先来了解一下二进制可执行文件和目标文件。我们知道程序运行起来后就有了进程，所以了解程序的结构对于认识操作系统大有好处。先抛三个问题：**编译器编译代码后生成的文件是目标文件，目标文件里面是什么？可执行文件里面又是什么？可执行文件与目标文件的区别在哪呢？**

这篇文件会使用如下一个简单的 C 文件进行贯穿讲解：

```c
#include <stdio.h>

int main(){
    printf("hello");
    return 0;
}
```

## 1.编译过程

代码要想成为可执行文件就需要经过编译。编译过程分为预处理、编译、汇编、链接。

> **预处理**：主要处理源码中的预处理指令，在 C/C++ 中主要指的是“#include”、“#define” 等，它的处理方法是将这些指令所在的位置进行内容替换，比如 “#include” 就会把整个头文件给引进来。
>
> **编译**：把上一步预处理过的文件进行词法分析、语法分析、语义分析&优化一系列步骤后生成汇编代码。这是整个编译过程中最难最复杂的 部分。在现在版本的 GCC 中，预处理和编译这两个过程已经合并为一步。
>
> **汇编**：把汇编代码转变为机器可以执行的指令，这是一个翻译的过程，有个汇编指令与机器指令的对照表进行一一对应。
>
> **链接**：把每个独立编译的模块进行组装，这些模块之间会有相互引用，链接就是让这些独立模块能够相互正确引用，形成一个完整的可执行文件。链接分为动态链接和静态链接，这两个会在下篇文章进行讲解。

使用以上那个 C 文件进行整个编译过程如下：

不想这么麻烦的话，直接一行代码搞定：

```shell
gcc hello.c -o hello
```

## 2.什么是可执行文件

**可执行文件指的是可以由操作系统进行加载执行的文件**。在不同的系统中，可执行文件是不同的。比如在 windows 中可执行文件是以 exe 后缀的文件，而在 linux 中可执行文件可以是任何后缀的文件，只是需要给文件添加“可执行”权限。注意，在同一种操作系统中如果 CPU 的架构不同的话，可执行文件是不能通用的，比如：在 linux 中，ARM 架构上的可执行文件就不能直接在 X86 架构执行，因为可执行文件内通常含有二进制编码的 CPU 指令，而每种 CPU 的指令集都是不一样的。这种情况下通常需要进行交叉编译：在一个平台上生成另一个平台的可执行文件，比如在 X86 平台上生成 ARM 的可执行文件。

## 3.可执行文件类型

**在 windows 系统中可执行文件类型为 PE(Portable Executable)，在 Linux 系统中可执行文件类型为 ELF(Executable Linkable Format)**。

在 linux 下可以使用 file 命令查看文件的文件格式。

普通的源码文件显示是：

```shell
[root@centos7-dev hello]# file hello.c 
hello.c: C source, ASCII text
```

目标文件是：

```shell
[root@centos7-dev hello]# file hello.o 
hello.o: ELF 64-bit LSB relocatable, x86-64, version 1 (SYSV), not stripped
```

可执行文件是：

```shell
[root@centos7-dev hello]# file hello 
hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=6115b831b9be5d023a87ce84ecd72d44cbfa1548, not stripped
```

不是只有可执行文件可以按照上述这两种类型进行存储，目标文件(.obj/.o)可以，链接库（.dll/.so）也可以，甚至在 linux 下的 coredump 也是可执行文件格式。目标文件既然和可执行文件的存储结构一致，那两者有什么差异呢？一句话：**目标文件是尚未进行链接操作的可执行文件**。

## 4.关于链接

为什么会有链接这个动作呢？

先把时间拉回到打孔条带的时代，在计算机刚出来的时候，程序都是写在条带上。最开始的程序是一条纸带，很简单很完整。然后随着时间的推移，程序越来越多了，条带也是越来越多。在某个时刻有个程序员想偷懒，他正在写的 A 程序想使用之前写好的 B 程序的一个功能，所以他把 B 条带上的那段代码给剪出来然后拼接上了 A 程序。这是最早的链接，应该也是最早的静态链接。

在现代的软件开发过程中，程序文件的数量是非常庞大的，往往一个工程就有上千个模块和文件。这些模块和文件是相互独立和依赖的，相互调用的情况是很常见的。你想想，这样一个庞大的项目要编译出一个可执行文件应该怎么做？先对每个代码文件进行单独编译得到目标文件，然后把这些目标文件给捏到一块去，形成一个可执行文件。而这个捏到一块去的过程就是链接。

如果没有这个链接过程，上千模块的目标文件根本没法正常工作，因为它们无法执行。那有人说了：整个项目工程可以只写一个文件呀，只对这个文件进行编译就可以不用链接了。没错，但那就没法比较好的进行多人协作开发了。

## 5.ELF 结构

由于对 windows 系统的可执行文件结构不熟，所以这里拿 linux 系统下的可执行文件结构 ELF 进行分析一下。**ELF 里面包含了编译后的机器指令和数据、符号表、调试信息、字符串等，这些不同类型的信息都会单独分开存放在某个模块内，一般称呼这些模块为”段“**。

首先我们得先了解 ELF 都有哪些段，请看如下资料：

```
.bss
构成程序的内存映像的未初始化数据。根据定义，系统在程序开始运行时会将数据初始化为零。如节类型 SHT_NOBITS 所指明的那样，此节不会占用任何文件空间。

.comment
注释信息，通常由编译系统的组件提供。

.data、.data1
构成程序的内存映像的已初始化数据。

.dynamic
动态链接信息。

.dynstr
进行动态链接所需的字符串，通常是表示与符号表各项关联的名称的字符串。

.dynsym
动态链接符号表。

.eh_frame_hdr、.eh_frame
用于展开栈的调用帧信息。

.fini
可执行指令，用于构成包含此节的可执行文件或共享目标文件的单个终止函数。

.fini_array
函数指针数组，用于构成包含此节的可执行文件或共享目标文件的单个终止数组。

.got
全局偏移表。

.hash
符号散列表。

.init
可执行指令，用于构成包含此节的可执行文件或共享目标文件的单个初始化函数。

.init_array
函数指针数组，用于构成包含此节的可执行文件或共享目标文件的单个初始化数组。

.interp
程序的解释程序的路径名。

.lbss
特定于 x64 的未初始化的数据。此数据与 .bss 类似，但用于大小超过 2 GB 的节。

.ldata、.ldata1
特定于 x64 的已初始化数据。此数据与 .data 类似，但用于大小超过 2 GB 的节。

.lrodata、.lrodata1
特定于 x64 的只读数据。此数据与 .rodata 类似，但用于大小超过 2 GB 的节。

.note
注释节中说明了该格式的信息。

.plt
过程链接表。

.preinit_array
函数指针数组，用于构成包含此节的可执行文件或共享目标文件的单个预初始化数组。

.rela
不适用于特定节的重定位。此节的用途之一是用于寄存器重定位。

.relname、.relaname
重定位信息，如重定位节中所述。如果文件具有包括重定位的可装入段，则此节的属性将包括 SHF_ALLOC 位。否则，该位会处于禁用状态。通常，name 由应用重定位的节提供。因此，.text 的重定位节的名称通常为 .rel.text 或 .rela.text。

.rodata、.rodata1
通常构成进程映像中的非可写段的只读数据。

.shstrtab
节名称。

.strtab
字符串，通常是表示与符号表各项关联的名称的字符串。如果文件具有包括符号字符串表的可装入段，则此节的属性将包括 SHF_ALLOC 位。否则，该位会处于禁用状态。

.symtab
符号表，如符号表节中所述。如果文件具有包括符号表的可装入段，则此节的属性将包括 SHF_ALLOC 位。否则，该位会处于禁用状态。

.symtab_shndx
此节包含特殊符号表的节索引数组，如 .symtab 所述。如果关联的符号表节包括 SHF_ALLOC 位，则此节的属性也将包括该位。否则，该位会处于禁用状态。

.tbss
此节包含构成程序的内存映像的未初始化线程局部数据。根据定义，为每个新执行流实例化数据时，系统都会将数据初始化为零。如节类型 SHT_NOBITS 所指明的那样，此节不会占用任何文件空间。

.tdata、.tdata1
这些节包含已初始化的线程局部数据，这些数据构成程序的内存映像。对于每个新执行流，系统会对其内容的副本进行实例化。

.text
程序的文本或可执行指令。
```

有了以上资料，我们可以使用 objdump 命令查看 hello 可执行文件中都有哪些模块：

```shell
[root@centos7-dev hello]# objdump -h hello

hello:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .interp       0000001c  0000000000400238  0000000000400238  00000238  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .note.ABI-tag 00000020  0000000000400254  0000000000400254  00000254  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  2 .note.gnu.build-id 00000024  0000000000400274  0000000000400274  00000274  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  3 .gnu.hash     0000001c  0000000000400298  0000000000400298  00000298  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .dynsym       00000060  00000000004002b8  00000000004002b8  000002b8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  5 .dynstr       0000003f  0000000000400318  0000000000400318  00000318  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  6 .gnu.version  00000008  0000000000400358  0000000000400358  00000358  2**1
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  7 .gnu.version_r 00000020  0000000000400360  0000000000400360  00000360  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  8 .rela.dyn     00000018  0000000000400380  0000000000400380  00000380  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  9 .rela.plt     00000048  0000000000400398  0000000000400398  00000398  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 10 .init         0000001a  00000000004003e0  00000000004003e0  000003e0  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 11 .plt          00000040  0000000000400400  0000000000400400  00000400  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 12 .text         00000182  0000000000400440  0000000000400440  00000440  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 13 .fini         00000009  00000000004005c4  00000000004005c4  000005c4  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 14 .rodata       00000016  00000000004005d0  00000000004005d0  000005d0  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 15 .eh_frame_hdr 00000034  00000000004005e8  00000000004005e8  000005e8  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 16 .eh_frame     000000f4  0000000000400620  0000000000400620  00000620  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 17 .init_array   00000008  0000000000600e10  0000000000600e10  00000e10  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 18 .fini_array   00000008  0000000000600e18  0000000000600e18  00000e18  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 19 .jcr          00000008  0000000000600e20  0000000000600e20  00000e20  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 20 .dynamic      000001d0  0000000000600e28  0000000000600e28  00000e28  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 21 .got          00000008  0000000000600ff8  0000000000600ff8  00000ff8  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 22 .got.plt      00000030  0000000000601000  0000000000601000  00001000  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 23 .data         00000004  0000000000601030  0000000000601030  00001030  2**0
                  CONTENTS, ALLOC, LOAD, DATA
 24 .bss          00000004  0000000000601034  0000000000601034  00001034  2**0
                  ALLOC
 25 .comment      0000005a  0000000000000000  0000000000000000  00001034  2**0
                  CONTENTS, READONLY
```

可以发现目标文件内部的模块信息比可执行文件少很多，**这是因为在目标文件经过链接过程变成可执行文件的过程中进行很多处理，比如：地址和空间分配、重定位、符号决议等。**

我们还可以用 objdump 来查看每个模块的代码信息：

```shell
objdump -s -d hello
```

查看符号表：

```shell
objdump -x hello
```

更多关于 objdump 命令的使用可以 man objdump 查看。



欢迎关注公众号：哈扣。

![](https://i.loli.net/2021/03/12/Tt3uBvRqDQarMI2.jpg)

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/3.0/cn/80x15.png" /></a> 本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/">知识共享署名-非商业性使用-相同方式共享 3.0 中国大陆许可协议</a>进行许可。