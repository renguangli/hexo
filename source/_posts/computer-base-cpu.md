---
title: 计算机基础 - CPU
categories: 计算机基础
link_refer:
  - url: https://tech.hqew.com/news_2058653
    title: https://tech.hqew.com/news_2058653
  - url: https://software.intel.com/content/www/cn/zh/develop/articles/book-processor-architecture_cpu_function_and_composition.html
    title: https://software.intel.com/content/www/cn/zh/develop/articles/book-processor-architecture_cpu_function_and_composition.html
  - url: https://www.php.cn/faq/415789.html
    title: https://www.php.cn/faq/415789.html

date: 2020-10-05 22:35:59
---

CPU 是计算机的大脑, 也是整个计算机中最贵的配件。

<!-- more -->

# CPU 是什么 

![](/images/cpu-i7.jpg)

CPU 是 Central Processing Unit 的缩写，中文意思为中央处理器，那么中央处理器又是什么东西呢？

中央处理器（CPU）是一块超大规模的集成电路，是一台计算机的运算核心（Core）和控制核心（ Control Unit）。

# CPU 的功能

CPU 控制整个程序的执行，它具有以下基本功能：

## （1）程序控制

程序控制就是控制指令的执行顺序。
程序是指令的有序集合，这些指令的相互顺序不能任意颠倒，必须严格按程序规定的顺序执行。
保证计算机按一定顺序执行程序是 CPU 的首要任务。

## （2）操作控制

操作控制就是控制指令进行操作。
一条指令的功能往往由若干个操作信号的组合来实现。因此，CPU 管理并产生每条指令的操作信号，把各种操作信号送往相应的部件，从而控制这些部件按指令的要求进行操作。

## （3）时间控制

时间控制就是对各种操作实施定时控制。
在计算机中，各种指令的操作信号和一条指令的整个执行过程都受到严格定时。只有这样，计算机才能有条不紊地工作。

## （4）数据加工

数据加工就是对数据进行算术、逻辑运算。
完成数据的加工处理，是 CPU的 根本任务。

# CPU 的组成

![](/images/cpu-zc.jpg)

CPU 主要由运算器，控制器两部分组成

## 运算器

运算器是由算术逻辑单元（ALU）、累加器（ACC）、状态寄存器(PSW)、数据寄存(DR)器组等组成。

它有两个主要功能：
- 执行所有的算术运算；
- 执行所有的逻辑运算

## 控制器

控制器是整个计算机系统的指挥中心。在控制器的指挥控制下，运算器、存储器和输入/输出设备等部件协同工作，构成了一台完整的通用计算机。

控制器通常由程序计数器（PC）、指令寄存器（IR）、指令译码器（ID）、时序发生器和操作控制器组成。其主要功能包括
- 从主存中取出一条指令，并指出下一条指令在主存中的位置；
- 对指令进行译码，并产生相应的操作控制信号，以便启动规定的动作；
- 指挥并控制 CPU、主存和输入/输出设备之间数据流动的方向。

## CPU 中的主要寄存器

**数据寄存器**（Data Register，DR）主要作为CPU和主存、外设之间信息传输的中转站，用以弥补CPU和主存、外设之间操作速度上的差异

数据寄存器用来暂时存放由主存储器读出的一条指令或一个数据字；反之，当向主存存入一个数据字时，也暂时将它们存放在数据寄存器中。

数据寄存器的作用是：

- 作为 CPU 和主存、外围设备之间信息传送的中转站；
- 弥补 CPU 和主存、外围设备之间在操作速度上的差别；
- 在单累加器结构的运算器中，数据寄存器还可兼作操作数寄存器。

**指令寄存器**（Instruction Register，IR）用来存储 CPU 当前正在执行的一条指令。

**程序计数器**（Program Counter，PC）用来存储下一条指令地址。

**地址寄存器**（Address Register，AR）用来保存 CPU 当前所访问的主存单元的地址

**累加寄存器**通常简称累加器（Accumulator，AC），是一个通用寄存器。

累加器的功能是：当运算器的算术逻辑单元(ALU)执行算术或逻辑运算时，为 ALU 提供一个工作区，可以为 ALU 暂时保存一个操作数或运算结果。

**程序状态字寄存器**（Program Status Word，PSW）用来表征当前运算的状态及程序的工作方式。

## 微操作与数据通路

控制器在实现一条指令的功能时，总是把每一条指令分解成一系列时间上先后有序的最基本、最简单、不可再分的操作控制动作，这种最基本、最简单、不可再分的操作称为微操作（Microoperation）。
我们通常把许多寄存器之间传输信息的通路称为数据通路（Data Path），它控制信息从什么地方开始，中间经过哪个寄存器或多路开关，最后传送到哪个寄存器。
在数据通路中，微操作通过自身的控制作用和彼此之间的密切配合，使指令流、数据流等信息流按照预定的路径流动，以实现指令的功能。每一条指令的功能决定了它所需要的一系列带时序的微操作信号。

## 操作控制器

控制器的基本功能是负责指令的读出、识别和解释，并指挥协调各功能部件执行指令。

操作控制器是CPU中完成取指令和执行指令全过程的部件，其主要功能是根据指令操作码和时序信号的要求，产生各种操作控制信号，以便在各寄存器之间正确地建立数据通路，从而完成取指令和执行指令的控制。

操作控制器有组合逻辑控制器和微程序控制器两种，二者的差别在于其中的控制信号形成部件不同，进而反映出不同的设计原理和方法。

根据设计方法不同，又可将组合逻辑控制器进一步细分为硬连线控制器和门阵列控制器。

## 时序信号发生器

CPU中除了操作控制器外，还必须有时序信号发生器。

由于计算机的高速工作，每一个动作的时间必须非常严格，不能有任何差错。时序发生器的作用，就是对操作控制器产生的各种控制信号实施时间上的严格控制，产生各功能部件所需要的定时控制信号。

# CPU 指令执行过程

CPU 是如何执行一条条指令的呢？

几乎所有的冯诺依曼型的计算机的 CPU，其工作都可以分为 5 个阶段：

- 取指令：将内存中的指令读取到 CPU 的寄存器中，程序计数器存储下一条指令地址
- 指令译码：指令读取完成后，指令译码器按照预定的指令格式，对取回的指令进行拆分和解释，识别出不同的指令类型以及各种获取操作数的方法。
- 指令执行：译码完成后，开始执行指令，完成指令所规定的各种操作，具体实现指令的功能。
- 访问取数：根据指令的需要，可能去内存中读取数据，根据指令地址码，得到操作数在主存中的地址，从主存中读取该操作数用于运算。
- 结果写回：把执行指令的阶段的执行结果数据写回到某种存储设备上，一般写回到 CPU 的内部寄存器中，便于后续的指令快速读取。

# CPU 的性能

CPU 性能的主要受以下三大方面的因素影响。

-  **CPU的主频**：CPU 的主频大小，代表着 CPU 运算能力的大小。CPU 的主频越高，处理数据能力就越强，比如 2GHz 的 CPU 的性能是 1GHz 的CPU的两倍。
- **CPU的核数**：CPU 的核数多少，代表着 CPU 运算能力的高低。CPU 的核数越多，处理数据能力就越强，单核的 CPU 相当于只有一个大脑，双核的 CPU 相当于有两个大脑，多核 CPU 相当于有多个大脑。因此，主频相同的情况下，双核的 CPU 的性能是单核的CPU的两倍。
- **CPU的缓存**：工作时，CPU 往往需要从内存和硬盘中重复读取同样的数据块，CPU 的缓存容量越大，就能大幅度提升 CPU 内部读取数据的命中率，从而不用再到内存或者硬盘上寻找，以此提高系统性能。因此，CPU 的缓存越大越好。

CPU 性能天梯图

<http://www.mydrivers.com/zhuanti/tianti/cpu/index.html>