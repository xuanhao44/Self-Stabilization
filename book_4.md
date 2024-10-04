# 4 Self-Stabilizing Algorithms for Model Conversions

Previous chapters considered several models of distributed computation. Such models can differ in several aspects. One aspect is whether shared memory or message passing is used for communication; another is whether the existence of a central daemon or a distributed daemon scheduler is assumed, or an interleaving of read/write actions on shared registers (as described in section 2.1). The existence of unique identifiers, a single special processor, or neither of the above (i.e., anonymous processors) can be assumed. In this chapter we design techniques for converting a self-stabilizing algorithm that is designed for one system to work on another system. The designer of an algorithm may then choose the best platform for the algorithm design process and then apply the algorithm to another system with different settings. The framework of algorithm conversion is an opportunity to study self-stabilizing algorithms for fundamental tasks.

前几章考虑了几种分布式计算模型。这些模型在几个方面可能有所不同。一个方面是使用共享内存还是消息传递进行通信；另一个方面是假设存在中央守护进程还是分布式守护进程调度程序，或者是对共享寄存器的读/写操作交错（如第 2.1 节所述）。可以假设存在唯一标识符、单个特殊处理器，或以上都没有（即匿名处理器）。在本章中，我们设计了将为一个系统设计的自稳定算法转换为在另一个系统上运行的技术。算法设计者可以选择最适合算法设计过程的平台，然后将算法应用于具有不同设置的另一个系统。算法转换框架是研究基本任务的自稳定算法的一个机会。

- [4.1 Token-Passing: Converting a Central Daemon to read/write](book_4_1.md)
- [4.2 Data-Link Algorithms: Converting Shared Memory to Message Passing](book_4_2.md)
- [4.3 Self-Stabilizing Ranking: Converting an Id-based System to a Special-processor System](book_4_3.md)
- [4.4 Update: Converting a Special Processor to an Id-based Dynamic System](book_4_4.md)
