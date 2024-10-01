# 2.3 Complexity Measures

The complexity measures used to evaluate an algorithm include time complexity and space (memory) complexity. At first glance, the attempt to define the time complexity of asynchronous systems may seem to contradict the asynchronous nature of the system. By the definition of asynchronous systems, there is no bound on the rate/speed of step-executions/message-arrivals. However, in order to evaluate and compare different asynchronous algorithms, it is convenient to use the number of *asynchronous rounds* to measure the time complexity of a particular execution. The first *asynchronous round* (or *round*) in an execution $E$ is the shortest prefix $E'$ of E **such that each processor executes at least one step in $E'$**. Let $E''$ be the suffix of $E$ that follows $E'$, $E = E'E''$. The second round of $E$ is the first round of $E''$, and so on. The number of rounds in the execution of an algorithm is used to measure the time complexity of the algorithm.

用于评估算法的复杂度度量包括时间复杂度和空间（内存）复杂度。乍一看，试图定义异步系统的时间复杂度似乎与系统的异步性质相矛盾。根据异步系统的定义，步骤执行/消息到达的速率/速度没有上限。然而，为了评估和比较不同的异步算法，使用 *异步轮* 数来衡量特定执行的时间复杂度是很方便的。执行 $E$ 中的第一个 *异步轮*（或 *轮*）是 $E$ 的最短前缀 $E'$，使得**每个处理器在 $E'$ 中至少执行一步**。设 $E''$ 为 $E$ 的后缀，紧随 $E'$ 之后，$E = E'E''$。$E$ 的第二轮是 $E''$ 的第一轮，依此类推。算法执行中的轮数用于衡量算法的时间复杂度。

Intuitively, **the definition of an asynchronous round nullifies the speed differences of the processors by stretching the round to be long enough to include a step (including a communication operation) of the slowest processor in this execution segment. Thus, information can be transferred through the slowest processor even if that processor resides in a node that can separate the communication graph.** Moreover, if the speeds of the processors are identical and the speeds of message transmission are also identical, every asynchronous round elapses in the same constant time interval.

直观地说，**异步轮的定义通过将轮延长到足够长，以包括此执行段中最慢处理器的一步（包括通信操作），从而消除了处理器速度的差异。因此，即使该处理器位于可以分隔通信图的节点中，信息也可以通过最慢的处理器传输。**此外，如果处理器的速度相同且消息传输的速度也相同，则每个异步轮在相同的恒定时间间隔内经过。

**A self-stabilizing algorithm never terminates, and processors must repeatedly communicate with their neighbors.** In the shared-memory model, processors must repeatedly read the registers of their neighbors and in the message passing model, processors must continue to send and receive messages forever.

The following argument is used to explain why termination cannot be achieved: assume that every processor $P_i$ has a state $s_i$ in which $P_i$ is **terminated**. By the self-stabilizing property of the algorithm, the system must reach a safe configuration from any initial configuration. When the system is started in a configuration $c$ in which every processor $P_i$ is in state $s_i$, no processor executes any step, and thus $c$ must be a safe configuration. Therefore the task of the algorithm is achieved when every processor $P_i$ has only one state, namely the state $s_i$. Obviously, such tasks do not require any communication between the processors and the "algorithm" that is used is not a distributed algorithm.

**自稳定算法永不终止，处理器必须反复与其邻居通信。**在共享内存模型中，处理器必须反复读取其邻居的寄存器；在消息传递模型中，处理器必须不断发送和接收消息。

以下论点用于解释为什么无法实现终止：假设每个处理器 $P_i$ 有一个状态 $s_i$，其中 $P_i$ **终止**。根据算法的自稳定性，系统必须从任何初始配置达到安全配置。当系统在配置 $c$ 中启动时，其中每个处理器 $P_i$ 都处于状态 $s_i$，没有处理器执行任何步骤，因此 $c$ 必须是一个安全配置。因此，当每个处理器 $P_i$ 只有一个状态，即状态 $s_i$ 时，算法的任务就完成了。显然，这样的任务不需要处理器之间的任何通信，并且所使用的“算法”不是分布式算法。

The non-termination property can be easily identified in the code of a self-stabilizing algorithm: this code is usually a do forever loop that contains communication operations with the neighbors. For example, **in the shared memory case, the code of the algorithm for a processor $P_i$ usually starts with read operations of the communication registers of $P_i$ and then local computations that are followed by write operations in the communication registers of  $P_i$. The number of steps required to execute a single iteration of such a do forever loop is $O(\triangle)$, where $\triangle$ is an upper bound on the degree (number of neighbors) of  $P_i$**. In some of the proofs, it is very convenient to consider the configuration that follows at least one complete execution of an iteration of the do forever loop by every processor. **Note that a processor can be started in (a state in which it is in) the middle of executing an iteration of the do forever loop. However, if $x$ is the number of steps required to complete an iteration of the do forever loop, then fewer than $2x$ steps are required to complete an iteration of the loop (from the beginning of the loop to its end) when $P_i$ is started in an arbitrary state.**

自稳定算法的非终止特性可以很容易地在代码中识别出来：这段代码通常是一个包含与邻居通信操作的永久循环。例如，**在共享内存情况下，处理器 $P_i$ 的算法代码通常以读取 $P_i$ 的通信寄存器操作开始，然后是本地计算，接着是写入 $P_i$ 的通信寄存器操作。执行这样一个永久循环的单次迭代所需的步骤数是 $O(\triangle)$，其中 $\triangle$ 是 $P_i$ 的度（邻居数量）的上限。**在一些证明中，考虑每个处理器至少完整执行一次永久循环迭代后的配置是非常方便的。注意，**处理器可以在执行永久循环迭代的中间状态启动。然而，如果完成永久循环迭代所需的步骤数为 $x$，那么当 $P_i$ 从任意状态启动时，完成循环迭代（从循环的开始到结束）所需的步骤数少于 $2x$。**

For the sake of readability, we extend the definition of an asynchronous round to an asynchronous *cycle* when convenient. **The first *asynchronous cycle* (or *cycle*) in an execution $E$ is the shortest prefix $E'$ of $E$ such that each processor executes at least one complete iteration of its do forever loop in $E'$. Let $E''$ be the suffix of $E$ that follows $E'$, $E = E'E''$. The second cycle of $E$ is the first cycle of $E'$, and so on.**

为了便于阅读，我们在方便时将异步轮的定义扩展为异步 *周期*。**执行 $E$ 中的第一个 *异步周期*（或 *周期*）是 $E$ 的最短前缀 $E'$，使得每个处理器在 $E'$ 中至少执行一次其永久循环的完整迭代。设 $E''$ 为 $E$ 的后缀，紧随 $E'$ 之后，$E = E'E''$。$E$ 的第二个周期是 $E''$ 的第一个周期，依此类推。**

Note that if the do forever iteration consists of reading the communication registers of the neighbors, local computations, and writing to the communication registers, then each cycle spans $O(\triangle)$ rounds.

请注意，如果永久循环的迭代包括读取邻居的通信寄存器、本地计算和写入通信寄存器，那么每个周期跨越 $O(\triangle)$ 轮。

The time complexity of a synchronous algorithm is the number of pulses in the execution (which corresponds to the number of rounds).

同步算法的时间复杂度是执行中的脉冲数（对应于轮数）。

The space complexity of an algorithm is the total number of (local and shared) memory bits used to implement the algorithm.

算法的空间复杂度是用于实现算法的（本地和共享）内存位的总数。
