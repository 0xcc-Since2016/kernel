驱动程序在 Linux 内核里扮演着特殊的角色. 它们是截然不同的"黑盒子", 使硬件的特殊的一部分响应定义好的内部编程接口. 它们完全隐藏了设备工作的细节. 用户的活动通过一套标准化的调用来进行.  
  
驱动可以与内核的其他部分分开建立, 并在需要的时候在运行时"插入". 这种模块化使得 Linux 驱动易写, 以致于目前有几百个驱动可用.  
  
Kernel Structure:  
  
进程管理
内核负责创建和销毁进程, 并处理它们与外部世界的联系(输入和输出). 不同进程间通讯(通过信号, 管道, 或者进程间通讯原语)对整个系统功能来说是基本的, 也由内核处理. 另外, 调度器, 控制进程如何共享 CPU, 是进程管理的一部分. 更通常地, 内核的进程管理活动实现了多个进程在一个单个或者几个 CPU 之上的抽象.

内存管理
计算机的内存是主要的资源, 处理它所用的策略对系统性能是至关重要的. 内核为所有进程的每一个都在有限的可用资源上建立了一个虚拟地址空间. 内核的不同部分与内存管理子系统通过一套函数调用交互, 从简单的 malloc/free 对到更多更复杂的功能.

文件系统
Unix 在很大程度上基于文件系统的概念; 几乎 Unix 中的任何东西都可看作一个文件. 内核在非结构化的硬件之上建立了一个结构化的文件系统, 结果是文件的抽象非常多地在整个系统中应用. 另外, Linux 支持多个文件系统类型, 就是说, 物理介质上不同的数据组织方式. 例如, 磁盘可被格式化成标准 Linux 的 ext3 文件系统, 普遍使用的 FAT 文件系统, 或者其他几个文件系统.

设备控制
几乎每个系统操作最终都映射到一个物理设备上. 除了处理器, 内存和非常少的别的实体之外, 全部中的任何设备控制操作都由特定于要寻址的设备相关的代码来进行. 这些代码称为设备驱动. 内核中必须嵌入系统中出现的每个外设的驱动, 从硬盘驱动到键盘和磁带驱动器. 内核功能的这个方面是本书中的我们主要感兴趣的地方.

网络
网络必须由操作系统来管理, 因为大部分网络操作不是特定于某一个进程: 进入系统的报文是异步事件. 报文在某一个进程接手之前必须被收集, 识别, 分发. 系统负责在程序和网络接口之间递送数据报文, 它必须根据程序的网络活动来控制程序的执行. 另外, 所有的路由和地址解析问题都在内核中实现.

  
内核动态模块：  

Linux 的众多优良特性之一就是可以在运行时扩展由内核提供的特性的能力. 这意味着你可以在系统正在运行着的时候增加内核的功能( 也可以去除 ).

每块可以在运行时添加到内核的代码, 被称为一个模块. Linux 内核提供了对许多模块类型的支持, 包括但不限于, 设备驱动. 每个模块由目标代码组成( 没有连接成一个完整可执行文件 ), 可以动态连接到运行中的内核中, 通过 insmod 程序, 以及通过 rmmod 程序去连接.

系统中任何安全检查都由内核代码强加上去. 如果内核有安全漏洞, 系统作为一个整体就有漏洞. 在官方的内核发布里, 只有一个有授权的用户可以加载模块; 系统调用 init_module 检查调用进程是否是有权加载模块到内核里. 因此, 当运行一个官方内核时, 只有超级用户[1]或者一个成功获得特权的入侵者, 才可以利用特权代码的能力.

[1] 从技术上讲, 只有具有 CAP_SYS_MODULE 权利的人才可以进行这个操作. 我们第 6 章讨论 capabilities .

操作系统的角色, 实际上, 是给程序提供一个一致的计算机硬件的视角. 另外, 操作系统必须承担程序的独立操作和保护对于非授权的资源存取. 这一不平凡的任务只有 CPU 增强系统软件对应用程序的保护才有可能.

模块的角色是扩展内核的功能; 模块化的代码在内核空间运行. 经常地一个驱动进行之前提到的两种任务: 模块中一些的函数作为系统调用的一部分执行, 一些负责中断处理.

常常, 当你查看内核 API 时, 你会遇到以双下划线(__)开始的函数名. 这样标志的函数名通常是一个低层的接口组件, 应当小心使用. 本质上讲, 双下划线告诉程序员:" 如果你调用这个函数, 确信你知道你在做什么."

反之, 如果你有一个模块名为 module.ko, 是来自 2 个源文件( 姑且称之为, file1.c 和 file2.c ), 正确的书写应当是:

obj-m := module.o
module-objs := file1.o file2.o

内核装载module
--

sys_init_module -- syscall -> vmalloc -> loadin.  

insmod  
modprobe  

