# 2_1_概念

包含 2.1- 2.3 章，以及 PPT 上内容。

<!-- TOC -->

- [2\_1\_概念](#2_1_概念)
  - [abstract model 抽象模型](#abstract-model-抽象模型)
  - [communication 通信方式](#communication-通信方式)
    - [message passing queue 消息传递队列](#message-passing-queue-消息传递队列)
  - [configuration 配置概念](#configuration-配置概念)
  - [interleaving model 交错模型](#interleaving-model-交错模型)
    - [假设并发不会影响](#假设并发不会影响)
  - [术语表示](#术语表示)
    - [step 步骤](#step-步骤)
    - [execution（执行），以及 fair execution（公平执行）](#execution执行以及-fair-execution公平执行)
    - [environment step 环境步骤](#environment-step-环境步骤)
  - [关于公平执行的拓展解释](#关于公平执行的拓展解释)
    - [The Communication Fairness (FC) Property 通信公平性](#the-communication-fairness-fc-property-通信公平性)
    - [Node Failures 节点故障](#node-failures-节点故障)
  - [同步消息传递和共享内存系统](#同步消息传递和共享内存系统)
    - [RDMA(Remote Direct Memory Access) 远程直接内存访问](#rdmaremote-direct-memory-access-远程直接内存访问)
  - [Self-Stabilization Requirements（重点）](#self-stabilization-requirements重点)
    - [Arbitrary Transient Faults 任意瞬态故障](#arbitrary-transient-faults-任意瞬态故障)
    - [legal behavior（合法执行） \& safe configuration（安全配置） \& self-stabilizing algorithm（自稳定算法）](#legal-behavior合法执行--safe-configuration安全配置--self-stabilizing-algorithm自稳定算法)
  - [算法的复杂度评估](#算法的复杂度评估)
    - [asynchronous round 异步轮](#asynchronous-round-异步轮)
    - [永不停止，反复通信](#永不停止反复通信)
    - [asynchronous cycle 异步周期](#asynchronous-cycle-异步周期)

<!-- /TOC -->

## abstract model 抽象模型

分布式系统是抽象模型。

A distributed system is modeled by a set of $n$ state machines called processors that communicate with each other. We usually denote the $i$ th processor in the system by $P_i$. Each processor can communicate with other processors, called its neighbors.

分布式系统由一组称为处理器的 $n$ 个状态机组成，它们相互通信。我们通常用 $P_i$ 表示系统中的第 $i$ 个处理器。每个处理器可以与其他处理器（称为其邻居）通信。

## communication 通信方式

> The communication between neighboring processors can be carried out by message passing or shared memory. Communication by writing in, and reading from, the shared memory usually fits systems with processors that are geographically close together, such as multiprocessor computers or processes executed by a multitasking single-processor computer. A message-passing distributed model fits both processors that are located close to each other and wide-area distributed systems, such as communication networks.
>
> 相邻处理器之间的通信可以通过消息传递或共享内存进行。通过写入和读取共享内存进行通信通常适用于地理位置接近的处理器系统，例如多处理器计算机或由多任务单处理器计算机执行的进程。消息传递分布式模型适用于彼此靠近的处理器和广域分布式系统，例如通信网络。

两种通信方式：

- 共享内存：通过写入和读取共享内存进行通信通常适用于地理位置接近的处理器系统，例如多处理器计算机或由多任务单处理器计算机执行的进程。
- 消息传递：消息传递分布式模型适用于彼此靠近的处理器和广域分布式系统，例如通信网络。

### message passing queue 消息传递队列

> A unidirectional communication link from processor $P_i$ to processor $P_j$ transfers messages from $P_i$ to $P_j$. The abstraction used for such a unidirectional link is a first-in first-out (FIFO) queue $q_{i,j}$, that contains all messages sent by a processor $P_i$ to its neighbor $P_j$ that have not yet been received.
>
> 从处理器 $P_i$ 到处理器 $P_j$ 的单向通信链路将消息从 $P_i$ 传输到 $P_j$。用于这种单向链路的抽象是先进先出（FIFO）队列 $q_{i,j}$，它包含了所有由处理器 $P_i$ 发送给其邻居 $P_j$ 但尚未被接收的消息。

消息传递：异步分布式系统中——先进先出队列 $q_{i,j}$。

## configuration 配置概念

Definition:

- A full description of a message passing distributed system at a particular time consists of the state of every processor and the content of every queue (messages pending in the communication links). The term *system configuration* (or *configuration*) is used for such a description.
  - A configuration is denoted by $c = (s_1, s_2, ..., s_n, q_{1,2}, q_{1,3}, ..., q_{i,j}, ..., q_{n-1,n})$, where $s_i$, $1 \leq i \leq n$, is the state of $P_i$ and $q_{i,j}$, $i ≠ j$, is the queue of messages sent by $P_i$ to $P_j$ but not yet received.
- In the shared memory model, processors communicate by the use of shared communication registers (hereafter *registers*). Processors may write in a set of registers and may read from a possibly different set of registers. The configuration of the system consists of the state of all processors and the contents of the registers.
  - A configuration with $n$ processors and $m$ communication registers is denoted by $c = (s_1, s_2, ..., s_n; r_1, r_2, ..., r_m)$, where $s_i$, $1 \leq i \leq n$, is the state of $P_i$ and for $1 \leq j \leq m$, $r_j$ is the contents of a communication register.

配置的定义：

- 在消息传递模型中：在特定时间对消息传递分布式系统的完整描述包括每个处理器的状态和每个队列的内容（通信链路中未处理的消息）。这种描述称为系统配置（或配置）。
  - 配置表示为 $c = (s_1, s_2, ..., s_n, q_{1,2}, q_{1,3}, ..., q_{i,j}, ..., q_{n-1,n})$，其中 $s_i$，$1 \leq i \leq n$，是 $P_i$ 的状态，$q_{i,j}$，$i ≠ j$，是 $P_i$ 发送给 $P_j$ 但尚未接收的消息队列。
- 在共享内存模型中：处理器通过使用共享通信寄存器（以下简称寄存器）进行通信。处理器可以写入一组寄存器，并且可以从可能不同的一组寄存器中读取。系统的配置包括所有处理器的状态和寄存器的内容。
  - 具有 $n$ 个处理器和 $m$ 个通信寄存器的配置表示为 $c = (s_1, s_2, ..., s_n; r_1, r_2, ..., r_m)$，其中 $s_i$，$1 \leq i \leq n$，是 $P_i$ 的状态，对于 $1 \leq j \leq m$，$r_j$ 是通信寄存器的内容。

## interleaving model 交错模型

> **In other words, scheduling of events in a distributed system influences the transitions made by the processors. The situation is even more complicated, since processors execute program statements in parallel at different rates.**
>
> **The interleaving model is used to reason about the behavior of the distributed system.** In this model it is assumed that, at each given time, only a single processor executes a *computation step* (also called an *atomic step*). **Each computation step consists of internal computation and a single communication operation: a send or receive in message passing systems and a write or read in shared memory systems.**
>
> Note that a computation step may consist of local computations (e.g., subtraction of the values of two local registers of the processors) in addition to the communication operation. Without loss of generality, the time at which all the local operations between two communication operations of a processor occur is assumed to be immediately before the second communication operation. Thus, **it is possible to assume that every state transition of a process is due to communication-step execution** (including all local computations that follow the previous step and precede the communication operation of the computation step).
>
> **执行（非交互式）程序的独立计算机的未来状态转换可以从其当前状态确定性地预测。分布式系统中事件的调度会影响处理器的转换。情况更加复杂，因为处理器以不同的速率并行执行程序语句。**
>
> **交错模型用于推理分布式系统的行为。**在该模型中，假设在每个给定时间，只有一个处理器执行一个计算步骤（也称为原子步骤）。**每个计算步骤包括内部计算和一个通信操作：在消息传递系统中是发送或接收，在共享内存系统中是写入或读取。**
>
> 注意，计算步骤除了通信操作外，还可能包括本地计算（例如，处理器的两个本地寄存器值的减法）。在不失一般性的情况下，假设处理器的两个通信操作之间的所有本地操作发生的时间是紧接在第二个通信操作之前。因此，**可以假设进程的每个状态转换都是由于通信步骤的执行**（包括所有在前一步之后和计算步骤的通信操作之前的本地计算）。

计算步骤（原子步骤）构成：

- 内部计算（本地计算）：例如，处理器的两个本地寄存器值的减法。
- 通信操作：在消息传递系统中是发送或接收，在共享内存系统中是写入或读取。

关键假定：**可以假设进程的每个状态转换都是由于通信步骤的执行。**

### 假设并发不会影响

Note that **a distributed system allows the processors to execute steps concurrently**; however, **when processors execute steps concurrently we assume that there is no influence of one step on the other**. This is clearly true for send and receive operations that are executed simultaneously, because a message sent cannot be received by a receive operation that is executed at the same time. As for shared memory, it is assumed that the communication register architecture guarantees serialization: the read and write operations can be ordered in a total order such that the result of a read operation from some register is the value that was written last before this read (according to the total order) in that register.

请注意，**分布式系统允许处理器并发执行步骤**；然而，**当处理器并发执行步骤时，我们假设一个步骤不会对另一个步骤产生影响**。这对于同时执行的发送和接收操作显然是正确的，因为发送的消息不能被同时执行的接收操作接收。对于共享内存，假设通信寄存器架构保证了序列化：读和写操作可以按总顺序排列，使得从某个寄存器进行的读操作的结果是该寄存器中在此读操作之前最后写入的值（根据总顺序）。

## 术语表示

### step 步骤

denote a step (together with the identity of the processor that executes it) by $a$

Let $c_1$ and $c_2$ be two configurations of the system, **where $c_2$ is reached from $c_1$ by a single step $a$ of a processor; we denote this fact by $c_1 \xrightarrow{a} c_2$**.

The step $a$ is applicable to a configuration $c$ if (and only if) there exists a configuration $c'$ such that $c \xrightarrow{a} c'$.

用 $a$ 表示一个步骤（以及执行该步骤的处理器的身份）。

设 $c_1$ 和 $c_2$ 是系统的两个配置，其中 $c_2$ 通过处理器的单个步骤 $a$ 从 $c_1$ 达到；我们用 $c_1 \xrightarrow{a} c_2$ 表示这一事实。

当且仅当存在一个配置 $c'$ 使得 $c \xrightarrow{a} c'$ 时，步骤 $a$ 才适用于配置 $c$。

### execution（执行），以及 fair execution（公平执行）

An execution $E = (c_1, a_1, c_2, a_2,···)$ (in the interleaving model) is **an alternating sequence of configurations and steps** such that $c_{i-1} \xrightarrow{a_{i-1}} c_i$ ($i > 1$); in other words, the configuration $c_i$ ($i > 1$) is obtained from $c_{i-1}$ by the execution of step $a_{i-1}$.

A *fair* execution is **an execution in which every step that is applicable infinitely often is executed infinitely often**. In particular, if (infinitely often) a processor has a step to execute then the processor executes this step (infinitely often).

一个执行 $E = (c_1, a_1, c_2, a_2,···)$（在交错模型中）是**配置和步骤的交替序列**，使得 $c_{i-1} \xrightarrow{a_{i-1}} c_i$（$i > 1$）；换句话说，配置 $c_i$（$i > 1$）是通过执行步骤 $a_{i-1}$ 从 $c_{i-1}$ 获得的。

公平执行是指**每个无限次适用的步骤都被无限次执行的执行**。特别地，如果（无限次）一个处理器有一个步骤要执行，那么处理器就会执行这个步骤（无限次）。

### environment step 环境步骤

In a **message-passing system**, it is possible that a message will be lost during the execution of the algorithm; the reason is unreliable communication media that may lose or corrupt messages in transit. Error-detection codes are used to identify and discard corrupted messages, and these messages can be considered lost messages. To model such systems, we extend the definition of a step to include *environment steps* of type $loss_{i,j}(m)$.

The environment step $loss_{i,j}(m)$ is applicable to a configuration $c_k$ in which the queue $q_{i,j}$ contains the message $m$. The application of $loss_{i,j}(m)$ to $c_k$ results in a configuration $c_{k+1}$ in which $m$ is **removed** from $q_{i,j}$, and $c_k$ and $c_{k+1}$ are identical in the rest of their components.

Unlike steps executed by processors, we do not require that, in every infinite fair execution, the environment steps that are applicable infinitely often will be executed infinitely often. **We do require that, in a fair execution in which a message is sent infinitely often, the message must be received infinitely often.** To satisfy fairness the receive step must be executed infinitely often, while the loss step should not be executed infinitely often.

在**消息传递系统**中，算法执行过程中可能会丢失消息；原因是通信介质不可靠，可能会在传输过程中丢失或损坏消息。错误检测码用于识别和丢弃损坏的消息，这些消息可以被视为丢失的消息。为了对这种系统建模，我们扩展了步骤的定义，包括类型为 $loss_{i,j}(m)$ 的环境步骤。

环境步骤 $loss_{i,j}(m)$ 适用于队列 $q_{i,j}$ 包含消息 $m$ 的配置 $c_k$。将 $loss_{i,j}(m)$ 应用于 $c_k$ 会导致配置 $c_{k+1}$，其中 $m$ 从 $q_{i,j}$ 中**移除**，$c_k$ 和 $c_{k+1}$ 在其余组件中是相同的。

与处理器执行的步骤不同，我们不要求在每个无限公平执行中，无限次适用的环境步骤将被无限次执行。**我们确实要求，在一个消息无限次发送的公平执行中，该消息必须被无限次接收。**为了满足公平性，接收步骤必须被无限次执行，而丢失步骤不应被无限次执行。

## 关于公平执行的拓展解释

### The Communication Fairness (FC) Property 通信公平性

The Communication Fairness (FC) Property

- Communication Fairness: if a message is sent infinitely often, that message is received infinitely often
  - FC holds even if an infinite num. message are lost eventually,
  - Between any two successful deliveries, only a finite num messages may be lost, otherwise, the communication channel is faulty.

- 通信公平性：如果一个消息被无限频繁地发送，那么该消息被无限频繁地接收。
  - 即使最终丢失无限条消息，通信公平性也会保持，
  - 任意两次成功交付之间，只会有一定数量的消息丢失，否则通信通道故障。

---

个人觉得这个 PPT 上的表述和书中的描述有点出入，关键点在于丢失步骤是不是能又无限次。我问一下 GPT。

- **公平性要求的核心是“无限次发送消息必须被无限次接收”**，即在一个无限的时间段内，接收到的消息次数也是无限的，而不是总是丢失。
- **丢失无限条消息的描述**，并不是说所有消息都可以被丢失，而是说在无限次的尝试中仍可能存在无限次丢失。但这并不与公平性冲突，因为公平性要求的是接收的次数也是无限的。
- **任意两次成功交付之间的丢失数目是有限的**，则进一步限制了这种丢失行为，避免了过多的连续丢失导致的通信质量问题。

### Node Failures 节点故障

A processor that, eventually, **stops taking steps forever** is said to be **crashed**.

But how to distinguish, asynchronous networks, between a slow processor and a crashed one?

What is the maximum power we can grant to the adversary in "slowing down" processors without allowing the adversary to render a slow processor to be completely nonfunctional?

Every step that is applicable infinitely often is executed infinitely often. In other words, even if the HP Frontier is an exponentially growing number of steps more than the Arduino, the execution is still fair.

However, if any set of processors takes an infinite number of steps while the HP Frontier (or Arduino) takes no step, then the HP Frontier (respectively, Arduino) is crashed.

最终**永远停止**执行步骤的处理器被称为**崩溃**。

但是如何在区分异步网络中区分慢速处理器和崩溃的处理器呢？（回答：在异步网络中，区分一个慢速处理器和一个崩溃处理器是有挑战的，因为两者可能都表现得没有响应。关键区别在于，慢速处理器最终会执行步骤，尽管非常缓慢，而崩溃处理器则完全不会执行任何步骤。）

在“减慢”处理器速度的情况下，我们可以授予对手的最大能力是多少，而不允许对手使慢速处理器完全无法正常工作？

无限适用的每一步都被无限频繁地执行。换句话说，即使某些处理器（如 HP Frontier）比其他处理器（如 Arduino）快得多，执行仍然是公平的。

但是，**如果任何一组处理器执行了无限多的步骤，而另一个处理器（例如 HP Frontier 或 Arduino）没有执行任何步骤，那么后者被认为是崩溃的。**

## 同步消息传递和共享内存系统

**Yet there is a class of distributed algorithms designed for synchronous distributed systems in which a global clock pulse (or simply a *pulse*) triggers a simultaneous step of every processor in the system.** This class of synchronous algorithms fits multiprocessor systems in which the processors are located close to each other and can therefore be efficiently connected to a common clock pulse. Next we describe the assumptions concerning the steps of the processors in synchronous message-passing and shared-memory systems.

The following actions are performed between each successive pulses of a synchronous message-passing system: the pulse triggers message send operations of every processor to every one of its neighbors, then every message is received by its destination. In the shared memory system, a pulse triggers each processor to read all the registers of its neighbors. Once every processor has finished reading, the processors can write into their registers. Thus, since all the processors execute a step simultaneously, **the execution of a synchronous system $E = (c_1, c_2,···)$ is totally defined by $c_1$, the first configuration in $E$.**

有一类**为同步分布式系统设计的分布式算法**，其中**全局时钟脉冲（或简称脉冲）触发系统中每个处理器的同步步骤**。这类同步算法适用于处理器彼此靠近并且可以高效连接到公共时钟脉冲的多处理器系统。接下来，我们描述同步消息传递和共享内存系统中处理器步骤的假设。

在同步消息传递系统的每个连续脉冲之间执行以下操作：脉冲触发每个处理器向其所有邻居发送消息操作，然后每条消息被其目的地接收。在共享内存系统中，脉冲触发每个处理器读取其所有邻居的寄存器。一旦每个处理器完成读取，处理器就可以写入它们的寄存器。因此，由于所有处理器同时执行一个步骤，**同步系统的执行 $E = (c_1, c_2,···)$ 完全由 $c_1$（即 $E$ 中的第一个配置）定义。**

### RDMA(Remote Direct Memory Access) 远程直接内存访问

![RDMA](images/RDMA.png)

Typical latency numbers for RDMA and socket-based communication can vary significantly based on various factors, including hardware, network conditions, and the specific use case. However, some rough estimation can give you a general idea of the differences in latency.

1. **RDMA Latency is in microseconds Range**: RDMA can achieve extremely low latencies in the range of microseconds (μs). Latencies as low as 1-2 μs are possible with RDMA, especially when using high-performance network technologies like InfiniBand or RoCE.

2. **Socket-Based Communication Latency is in milliseconds Range**: Socket-based communication typically has higher latencies compared to RDMA. In a typical Ethernet-based network, socket communication latencies can be in the range of milliseconds (ms) or more, especially in scenarios with network congestion or when communication involves multiple layers of software.

It is important to note that these latency numbers are very rough estimates and can vary widely depending on the specific hardware, software stack, and network conditions. Additionally, advancements in both RDMA and socket-based communication technologies may lead to improvements or changes in these latency figures over time.

典型的 RDMA 和基于套接字的通信延迟数字可能会因各种因素而显著变化，包括硬件、网络条件和具体使用情况。然而，一些粗略的估计可以让你大致了解延迟的差异。

1. **RDMA 延迟在微秒范围内**：RDMA 可以实现极低的延迟，范围在微秒（μs）级别。使用高性能网络技术如 InfiniBand 或 RoCE 时，延迟可以低至 1-2 微秒。
2. **基于套接字的通信延迟在毫秒范围内**：与 RDMA 相比，基于套接字的通信通常具有更高的延迟。在典型的以太网网络中，套接字通信的延迟可以在毫秒（ms）级别或更高，特别是在网络拥塞或通信涉及多个软件层的情况下。

需要注意的是，这些延迟数字是非常粗略的估计，可能会因具体的硬件、软件栈和网络条件而有很大差异。此外，RDMA 和基于套接字的通信技术的进步可能会导致这些延迟数字随时间而改善或变化。

## Self-Stabilization Requirements（重点）

### Arbitrary Transient Faults 任意瞬态故障

- They represent any violation of the assumptions according to which the system and network were designed to behave
  - One may assume that CRC-codes always detect memory/messages corruption
  - If such corruption is undetected, stale info will propagate in the network

- An arbitrary combination of such failures can leave the network in an arbitrary state.

- If a network starts in an arbitrary configuration, how can the algorithm make the network present a legal behavior?
  - –*i.e.*, satisfy the problem definition?

- 它们代表了任何违反系统和网络设计行为假设的情况
  - 可以假设 CRC 码总是能检测到内存/消息的损坏
  - 如果这种损坏未被检测到，陈旧的信息将会在网络中传播
- 这种故障的任意组合可能会使网络处于任意状态
- 如果网络从任意配置开始，算法如何使网络呈现合法行为？
  - *即，满足问题定义？*

---

题目：

- Can a crashed processor ever take steps in a **non-self-stabilizing** network?
  - Yes, it may take a finite number of steps before it crashes, but not after it crashes.
- Can a crashed processor ever take steps in a **self-stabilizing** network?
  - Yes, it may take a finite number of steps before it crashes. We model as a transient fault the case in which a crashed processor **resume** and takes steps after it was crashed.

这两道题都对 crashed processor 的行为做出了评判，但是分别是非自稳定和自稳定网络。它们都会在崩溃前做出有限的操作，但是自稳定中网络中，崩溃的处理器崩溃后重新开始运作，我们把这个建模为瞬态故障。

这和前面说的：“最终**永远停止**执行步骤的处理器被称为**崩溃**”矛盾吗？不矛盾。只是说处理器最终会永远停止。（注：我能理解这个说法，但是我认为语言表述确实是有问题的）

### legal behavior（合法执行） & safe configuration（安全配置） & self-stabilizing algorithm（自稳定算法）

![legal_execution](images/legal_execution.png)

- A self-stabilizing system can be started in any arbitrary configuration and will eventually exhibit a desired "legal" behavior.
- We define the desired legal behavior by a set of legal executions denoted $LE$.
- Every system execution of a self-stabilizing system should have a suffix that appears in $LE$.
- A configuration $c$ is safe with regard to a task $LE$ and an algorithm if every fair execution of the algorithm that starts from $c$ belongs to $LE$.
- An algorithm is *self-stabilizing* for a task $LE$ if every fair execution of the algorithm reaches a safe configuration with relation to $LE$.

- 自稳定系统可以从任何任意配置开始，并最终表现出期望的“合法”行为。
- 我们通过一组合法执行（记为 $LE$）来定义期望的合法行为。
- 自稳定系统的每个系统执行都应该有一个**后缀**出现在 $LE$ 中。
  - 在这里，后缀指的是一个执行序列的最后一部分。自稳定系统的每个执行序列都应该有一个后缀出现在 $LE$ 中，这意味着无论系统如何开始，最终都会表现出合法行为。
- 对于任务 $LE$ 和算法，如果从配置 $c$ 开始的算法的每个公平执行都属于 $LE$，则配置 $c$ 对该任务是安全的。
  - 也就是说，**从这个配置开始，系统的每个配置都是安全的**。
  - 进一步的说，如果证明了一个配置是安全配置，那么在这之后的一连串配置都是安全配置（公平执行）。
- 如果算法的每个公平执行都达到与任务 $LE$ 相关的安全配置，则该算法对任务 $LE$ 是自稳定的。
  - 这意味着该算法能够确保系统最终会达到合法状态。

## 算法的复杂度评估

主要是在讨论时间复杂度。

### asynchronous round 异步轮

In order to evaluate and compare different asynchronous algorithms, it is convenient to use the number of *asynchronous rounds* to measure the time complexity of a particular execution. The first *asynchronous round* (or *round*) in an execution $E$ is the **shortest prefix** $E'$ of E **such that each processor executes at least one step in $E'$**. Let $E''$ be the suffix of $E$ that follows $E'$, $E = E'E''$. The second round of $E$ is the first round of $E''$, and so on. The number of rounds in the execution of an algorithm is used to measure the time complexity of the algorithm.

The definition of an asynchronous round nullifies the speed differences of the processors by stretching the round to be long enough to include a step (including a communication operation) of the slowest processor in this execution segment. Thus, information can be transferred through the slowest processor even if that processor resides in a node that can separate the communication graph.

为了评估和比较不同的异步算法，使用异步轮数来衡量特定执行的时间复杂度是很方便的。执行 $E$ 中的第一个异步轮（或轮）是 $E$ 的**最短前缀** $E'$，使得每个处理器在 $E'$ 中**至少执行一步**。设 $E''$ 为 $E$ 的后缀，紧随 $E'$ 之后，$E = E'E''$。$E$ 的第二轮是 $E''$ 的第一轮，依此类推。算法执行中的轮数用于衡量算法的时间复杂度。

异步轮的定义通过将轮延长到足够长，以包括此执行段中最慢处理器的一步（包括通信操作），从而消除了处理器速度的差异。因此，即使该处理器位于可以分隔通信图的节点中，信息也可以通过最慢的处理器传输。

---

例子：

E.g., let $P={p_i, p_j}$ and $E = (c_0, a^i_0, c_1, a^i_1, c_2, a^i_2, c_3, a^j_3, c_4, a^i_4, c_5, …)$

- where $a^k_x$ is the $x$ th step in execution $E$ taking by $p_k$.
  - The first asynchronous round is $E' = (c_0, a^i_0, c_1, a^i_1, c_2, a^i_2, c_3, a^j_3)$
  - The second asynchronous round in $E$ is the first asynchronous round in $E''=(c_4, a^i_4, c_5, …)$.

很明显的至少一步，但是没说不能多步，所以 $E$ 中尽管 $p_i$ 执行了三步（$a^i_0, a^i_1, a^i_2$），但是最终在 $a^j_3$ 后才结束。并且 $E'$ 没包含 $c_4$，是因为定义中没提到这个配置也在一轮中。

### 永不停止，反复通信

**A self-stabilizing algorithm never terminates, and processors must repeatedly communicate with their neighbors.** In the shared-memory model, processors must repeatedly read the registers of their neighbors and in the message passing model, processors must continue to send and receive messages forever.

In the shared memory case, the code of the algorithm for a processor $P_i$ usually starts with read operations of the communication registers of $P_i$ and then local computations that are followed by write operations in the communication registers of  $P_i$. The number of steps required to execute a single iteration of such a do forever loop is $O(\triangle)$, where $\triangle$ is an upper bound on the degree (number of neighbors) of  $P_i$.

Note that a processor can be started in (a state in which it is in) the middle of executing an iteration of the do forever loop. However, if $x$ is the number of steps required to complete an iteration of the do forever loop, then fewer than $2x$ steps are required to complete an iteration of the loop (from the beginning of the loop to its end) when $P_i$ is started in an arbitrary state.

**自稳定算法永不终止，处理器必须反复与其邻居通信。**在共享内存模型中，处理器必须反复读取其邻居的寄存器；在消息传递模型中，处理器必须不断发送和接收消息。

在共享内存情况下，处理器 $P_i$ 的算法代码通常以读取 $P_i$ 的通信寄存器操作开始，然后是本地计算，接着是写入 $P_i$ 的通信寄存器操作。执行这样一个永久循环的单次迭代所需的步骤数是 $O(\triangle)$，其中 $\triangle$ 是 $P_i$ 的度（邻居数量）的上限。

处理器可以在执行永久循环迭代的中间状态启动。然而，如果完成永久循环迭代所需的步骤数为 $x$，那么当 $P_i$ 从任意状态启动时，完成循环迭代（从循环的开始到结束）所需的步骤数少于 $2x$。

### asynchronous cycle 异步周期

The first *asynchronous cycle* (or *cycle*) in an execution $E$ is the shortest prefix $E'$ of $E$ such that each processor executes at least one complete iteration of its do forever loop in $E'$. Let $E''$ be the suffix of $E$ that follows $E'$, $E = E'E''$. The second cycle of $E$ is the first cycle of $E'$, and so on.

执行 $E$ 中的第一个异步周期（或周期）是 $E$ 的最短前缀 $E'$，使得每个处理器在 $E'$ 中**至少执行一次其永久循环的完整迭代**。设 $E''$ 为 $E$ 的后缀，紧随 $E'$ 之后，$E = E'E''$。$E$ 的第二个周期是 $E''$ 的第一个周期，依此类推。
