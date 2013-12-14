---
layout: post
title: CICS交易中间件介绍 
categories:
- Middleware
---

##CICS基础概念	
1. 任务管理（Task Management）
2. 资源管理（Resource Management）
3. 恢复管理（Recovery Management）
4. 内存管理（Storage Management）
5. 队列管理（Queue Management）
6. 终端管理（Terminal Management）
7. 数据库管理（Database Management）
8. 文件管理（File Management）
9. 程序管理（Program Management）
10. CICS通信等

##什么是CICS	
简单地说，CICS是一种中间件产品（Middleware）。它协助操作系统高效地处理业务交易，使操作系统无须关注这些复杂的交易负载，操作系统只需关注非业务的工作负载。  
CICS最大的贡献就是深入分析了实时事务处理系统中与业务逻辑无关的、只与系统运行有关的、具有共性的需求，把上述种种复杂的软件功能归纳起来，以服务器的形式帮助应用程序实现这些功能，在整个系统的运行过程中充当应用管理的角色。数据库服务器的作用是管理系统中的所有数据，而事务服务器的作用是管理系统中所有的应用及与应用相关的资源。服务器上的应用程序请求CICS的调度服务，在CICS的管理和协调下运，并访问数据库和文件。由于CICS集中管理与应用系统有关的所有资源，因此就能以最优化的方式运行，保证达到最优的整体性能。

##CICS内部架构1
CICS Region（区域）是CICS在z/OS（或OS/390）上的一个实例，是CICS系统的基本单位。Region由一组CICS系统程序、Region的所有配置信息、它所管理的各种资源（交易、程序、数据等）组成，是一个独立的CICS环境。
Region类似于进程，有一个能执行多个线程的地址空间（Address Space）。
Region上可以拥有许多资源，如交易、程序、终端等。
应用程序的失败，只影响它所在的CICS Region。

##CICS内部架构2	
在CICS内部，CICS Region被划分成多组Domain（域）。每一个Domain负责具体的管理功能。每个Domain包括Management Modules、Tables和Control Blocks。
每个Domain是按功能分割的各个部件，都有特定的资源和功能，这些资源包括交易、程序、终端等；功能包括文件访问、数据检索等。
Domain和Domain之间通信的接口称为Gate。由于每个Domain都只能对自己控制的资源进行访问，每个Domain都有特定的功能，因此，如果一个Domain需要另外一种Service，它会通过Gate来调用另外一个Domain。
Domain的具体解释：
http://book.51cto.com/art/201008/216065.htm
* DD-Directory Manager Domain：提供对其他域功能的寻找服务。
* DM-Domain Manager Domain：用于维护与各个域相关的所有永久性信息。
* DS-Dispatcher Domain：用于控制任务的运行调度。
* DU-Dump Domain：负责生成CICS交易dump或系统dump。
* GC-Global Catalog Domain：负责保存、维护其他域在系统运行过程中的信息，用于保障系统能有序重启。
* KE-Kernel Domain：是CICS的中心部件，是其他各种Domain之间互相调用的中间桥梁，同时负责管理CICS的预初始化，并参与系统崩溃时的恢复工作。
* LC-Local Catalog Domain：负责保存、维护其他域在系统运行过程中用于重新初始化的一些信息。
* LD-Loader Domain：负责控制联机程序的装载和使用。
* LM-Lock Manager Domain：负责CICS资源的锁定和排队功能。
* ME-Message Domain：负责处理CICS的各种消息，包括程序的各种错误信息即Abend信息。
* MN-Monitoring Domain：负责将任务的活动信息写到SMF中。
* PA-Parameter Manager Domain：负责保存系统初始化参数表（SIT）的内容。
* PG-Program Manager Domain：提供程序的控制功能，Link，Xctl，Load，Release，Return，Abend和条件的处理。
* RM-Recovery Manager Domain：负责进行恢复管理。
* SM-Storage Manager Domain：负责管理CICS的内存（DSA&EDSA）。
* ST-Statistics Manager Domain：负责将CICS的系统资源信息写到SMF。
* TI-Timer Domain：为其他域提供计时服务。
* TR-Trace Domain：负责记录CICS在运行过程的所有事件。
* XM-Transaction Manager Domain：提供交易的相关服务，如建立和终止任务、查询和清除任务、管理交易的定义和分类。
* XS-Security Manager Domain：与外部安全系统相连接，提供CICS相关安全服务。

##CICS底层
CICS的Transaction Isolation.
主机的交易切换机制类比进程切换机制，管理机制与Linux等通用平台应该是相通的。保存当前状态到寄存器。
但CICS在使用时完全不用考虑内存问题，全部丢给系统来做。一个TCB一次只能跑一个Task。
address space分配多个subspace，来达到isolation的效果。isolation可以通过编程解决。

在QR tcb下面可以看到很多的daughter tcb。多个J8、J9 tcb，代表多个CICS任务，使用JVM。CICS-DB2 attachment facility使用L8 tcb。除了L8、J8、J9，其它tcb都是CICS system tcb。

* FO是file owning tcb，用于各种文件操作
* RO是resource owning tcb，执行program load
* QR是cics main tcb，subdispatch应用程序
* CO是concurrent tcb，不同的domain用它进行I/O
* SZ用于FEPI
* RP用于ONC RPC call
* CAVM和XRF用于XRF support
* SL是socket domain listener tcb
* SO是socket domain使用的tcb，下面的多个S8 tcb用于SSL support

##CICS System Programmer	
System programmer
>Responsible for keeping CICS (and other systems) running, uses monitoring facilitiesto observe resource usage.

###CICS System Programming Interface - SPI
* EXEC CICS CREATE 
* EXEC CICS DISCARD
* EXEC CICS INQUIRE
* EXEC CICS PERFORM
* EXEC CICS SET

##CICS Application Programmer	
Application programmer
>Writes business applications, assumes system availability but would code for error conditions.

###CICS Application Programming Interface - API
* EXEC CICS
* EXEC CICS PUT CONTAINER
* EXEC CICS GET CONTAINER
* EXEC CICS WRITEQ TS
* EXEC CICS READQ TS