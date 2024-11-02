# 7.1 Superstabilization

Let us first study the combination of self-stabilization and dynamic systems. In a *dynamic* system, communication links and processors may fail and recover during normal operation. Algorithms for dynamic systems are designed to cope with such failures and to recover without global reinitialization. These algorithms consider only global states that are reachable from a predefined initial state under a *restrictive sequence of failures*; under such restrictions, the algorithms attempt to cope with failures with as few adjustments as possible. In contrast to self-stabilizing algorithms that were not designed to guarantee a particular behavior between the time of a transient fault and restoration to a legitimate state, dynamic algorithms make guarantees about behavior at all times (e.g., during the period between a failure event and the completion of necessary adjustments).

让我们首先研究自稳定性和动态系统的结合。在一个 *动态* 系统中，通信链路和处理器可能在正常操作期间发生故障并恢复。动态系统的算法旨在应对此类故障并在无需全局重新初始化的情况下恢复。这些算法只考虑从预定义的初始状态在 *限制性故障序列* 下可到达的全局状态；在这种限制下，算法尝试以尽可能少的调整应对故障。与未设计用于保证在瞬态故障和恢复到合法状态之间特定行为的自稳定算法相反，动态算法在任何时候都对行为进行保证（例如，在故障事件和完成必要调整之间的时期）。

Superstabilizing algorithms combine benefits of both self-stabilizing and dynamic algorithms. Roughly speaking, an algorithm is superstabilizing if it is self-stabilizing and if, when started in a safe configuration and a topology change occurs, it converges to a new safe configuration in a graceful manner. In particular, it should preserve a passage predicate during the convergence to a new safe configuration and should exhibit a fast convergence rate.

超稳定算法结合了自稳定和动态算法的优点。大致而言，如果一个算法是自稳定的，并且当从安全配置开始并发生拓扑变化时，它能优雅地收敛到新的安全配置，那么它就是超稳定的。特别地，在收敛到新的安全配置期间，它应保持一个通行谓词，并表现出快速的收敛速度。

The passage predicate is defined with respect to a class of topology changes. A topology change typically falsifies legitimacy. The passage predicate must therefore be weaker than legitimacy but strong enough to be useful; ideally, it should be the strongest predicate that holds when a legitimate state undergoes a topology change event.

- One example of a passage predicate is the existence of at most one token in the token-passing task; in a legitimate state, exactly one token exists, but a processor crash could lose the token but not falsify the passage predicate.
- Similarly, for the leader-election task, the passage predicate could specify that no more than one leader exists.

通行谓词是相对于一类拓扑变化定义的。一次拓扑变化通常会使合法性失效。因此，通行谓词必须比合法性弱一些，但足够强以保持有用；理想情况下，它应该是合法状态经历拓扑变化事件时保持的最强谓词。

- 一个通行谓词的例子是令牌传递任务中的最多存在一个令牌；在合法状态中，正好存在一个令牌，但处理器崩溃可能会丢失令牌，而不会使通行谓词失效。
- 类似地，对于领导者选举任务，通行谓词可以指定不超过一个领导者存在。

In addition to the complexity measures used to evaluate self-stabilizing algorithms, superstabilizing algorithms are also evaluated by their *superstabilizing time* and *adjustment measure*. The *superstabilizing time* is the maximum number of rounds it takes for an algorithm starting from a legitimate state, to undergo a single topology change and then reach a legitimate state. The *adjustment measure* is the maximum number of processors that must change their local states, upon a topology change from a legitimate state, so that the algorithm reaches a legitimate state.

除了用于评估自稳定算法的复杂性度量外，超稳定算法还通过其 *超稳定时间* 和 *调整量* 进行评估。*超稳定时间* 是指算法从合法状态开始，经历一次拓扑变化并达到合法状态所需的最大轮数。*调整量* 是指从合法状态经历一次拓扑变化后，为使算法达到合法状态而必须更改其本地状态的处理器的最大数量。

Many distributed algorithms have been designed to cope with continuous dynamic changes. These algorithms make certain assumptions about the behavior of processors and links during failure and recovery; for instance, most of them do not consider all possible combinations of crashes, assume that every corrupted message is identified and discarded, and assume bounded link capacity. Recall, that according to our proof in section 3.2, when crashes are possible and the link capacity is not known, an arbitrary configuration can be reached.

许多分布式算法被设计用于应对连续的动态变化。这些算法对处理器和链路在故障和恢复期间的行为做出了一些假设；例如，它们大多数不考虑所有可能的崩溃组合，假设所有被破坏的信息都能被识别并丢弃，并且假设链路容量是有界的。回想一下，根据我们在 3.2 节中的证明，当崩溃是可能的且链路容量未知时，可以达到任意配置。

The primary goal of self-stabilizing algorithms is to recover from transient faults, and this goal has influenced their design and analysis. For instance, for a correct self-stabilizing algorithm, there are no restrictions on the behavior of the system *during* the convergence period: the only guarantee is convergence to a legitimate state.

自稳定算法的主要目标是从瞬态故障中恢复，这一目标影响了它们的设计和分析。例如，对于一个正确的自稳定算法，在收敛期间系统的行为没有限制：唯一的保证是收敛到一个合法状态。

Self-stabilization’s treatment of a dynamic system differs from that of dynamic algorithms in how topology changes are modeled. Dynamic algorithms assume that topology changes are *events* signaling changes in incident processors. In particular, an initial state is defined for the processors executing a dynamic algorithm. When the system starts operating, each processor that joins the execution of the algorithm initializes its state.

自稳定性对动态系统的处理与动态算法在拓扑变化的建模上有所不同。动态算法假设拓扑变化是表示相关处理器变化的*事件*。特别地，为执行动态算法的处理器定义了初始状态。当系统开始运行时，加入算法执行的每个处理器会初始化其状态。

Self-stabilizing algorithms take a necessarily more conservative approach that is entirely state-based: a topology change results in a new state from which convergence to a legitimacy is guaranteed, with no use of signals of events. Yet, when the system is in a legitimate state and a fault happens to be detected, can the behavior during the convergence be constrained to satisfy some desired safety property? For instance, is it possible in these situations for the algorithm to maintain a “nearly legitimate” state during convergence? A self-stabilizing algorithm that does not ignore the occurrence of events will perform better: it will be initialized in a predefined way and react better to dynamic changes during the execution.

自稳定算法采用更保守的、完全基于状态的方法：拓扑变化导致一个新状态，从该状态保证收敛到合法性，而无需使用事件信号。然而，当系统处于合法状态且检测到故障时，可以约束收敛期间的行为以满足某些期望的安全属性吗？例如，在这些情况下，算法是否可以在收敛期间保持“接近合法”的状态？一个不忽略事件发生的自稳定算法将表现得更好：它将以预定义的方式初始化，并在执行过程中更好地响应动态变化。

The issue can be motivated by considering the problem of maintaining a spanning tree in a network. Suppose the spanning tree is used for virtual circuits between processors in the network. When a tree link fails, the spanning tree becomes disconnected, but, virtual circuits entirely within a connected component can continue to operate. In the superstabilizing approach, the system restores a spanning tree so that existing virtual circuits in the connected components remain operating; thus, a least-impact legitimate state can be realized by simply choosing a link to connect the components.

这个问题可以通过考虑在网络中维护生成树的问题来激励理解。假设生成树用于网络中处理器之间的虚拟电路。当一条树链路失效时，生成树变得不连通，但完全在连通组件内的虚拟电路可以继续运行。在超稳定方法中，系统恢复生成树，使连通组件中的现有虚拟电路继续运行；因此，可以通过简单地选择一条链路来连接组件，实现对现有状态最小影响的合法状态。

The time complexity of a self-stabilizing algorithm is the worst-case measure of the time spent to reach a legitimate state from an arbitrary initial state. But is this measure appropriate for self-stabilization for dynamic systems? Perhaps a better measure would be the worst case: starting from an arbitrary legitimate state, considering a topology change, and then measuring the time needed to reach a legitimate state again. This approach can be motivated by considering the probability of certain types of faults: while a transient fault is rare (but harmful), a dynamic change in the topology may be frequent. Note that the issue of time complexity is orthogonal to the issue of whether an algorithm converges while preserving a passage predicate.

自稳定算法的时间复杂度是从任意初始状态到达到合法状态所花费的最坏情况时间度量。但是，这种度量是否适用于动态系统的自稳定性？也许更好的度量是最坏情况：从任意合法状态开始，考虑一个拓扑变化，然后测量达到合法状态所需的时间。这种方法可以通过考虑某些类型故障的概率来激励理解：尽管瞬态故障是罕见的（但有害），但拓扑的动态变化可能是频繁的。注意，时间复杂度问题与算法是否在保持通行谓词的情况下收敛的问题是正交的。

We present a self-stabilizing and superstabilizing algorithm for the graph coloring task. The graph coloring task is to assign a $color$ value to each processor, such that no two neighboring processors are designated by the same color. The distributed coloring algorithms that are presented below do not cope with the theoretical difficulty of minimizing the number of colors. The algorithms use $\triangle+1$ colors, where $\triangle$ is an upper bound on a processor’s number of neighbors. The goal is to demonstrate the superstabilizing property and the complexity measures that are related to superstabilization. Each processor $P_i$ has a register field ${color}_i$.

我们提出了一个用于图着色任务的自稳定和超稳定算法。图着色任务是为每个处理器分配一个 $color$ 值，使得没有两个相邻的处理器被指定为相同的颜色。下面介绍的分布式着色算法并没有应对最小化颜色数量的理论难题。算法使用 $\triangle+1$ 种颜色，其中 $\triangle$ 是处理器邻居数量的上限。其目标是展示超稳定属性及与超稳定性相关的复杂度度量。每个处理器 $P_i$ 有一个寄存器字段 ${color}_i$。

A self-stabilizing coloring algorithm should ensure that, for every two neighboring processors $P_i$ and $P_j$, it eventually holds that ${color}_i \neq {color}_j$​ and that no color variable changes value.

一个自稳定的着色算法应确保，对于每两个相邻的处理器 $P_i$ 和 $P_j$，最终保持 ${color}_i \neq {color}_j$，且没有颜色变量改变值。

Let us first explain the code of the algorithm presented in figure 7.1. A processor $P_i$ has an internal variable $GColors$ that is used to store the set of colors of $P_i$'s neighbors. $GColors$ accumulates the colors of $P_i$'s neighbors that have an identifier greater than $i$ (this is done in lines 2 through 5 of the code).

首先解释图 7.1 中所示算法的代码。处理器 $P_i$ 有一个内部变量 $GColors$，用于存储 $P_i$ 的邻居的颜色集合。 $GColors$ 累积 $P_i$ 的邻居中标识符大于$i$的处理器的颜色（在代码的第 2 至第 5 行执行此操作）。

Then $P_i$ checks whether its color is that of one of the processors with a greater identifier and, if so, chooses a new color different from the colors of these processors (this is done in lines 7 and 8 of the code). Finally, $P_i$ writes the value of ${color}_i$ to $r_i.color$.

然后，$P_i$ 检查其颜色是否与标识符更大的处理器之一相同，如果是，则选择一个不同于这些处理器颜色的新颜色（在代码的第 7 和第 8 行执行此操作）。最后，$P_i$ 将 ${color}_i$ 的值写入 $r_i.color$。

![figure_7.1](images/figure_7.1.png)

Assume that a link between two processors is added or that a new processor is added to the system. Note that we assume that the above addition does not violate the restriction that each processor have no more than $\triangle$ neighbors. The new system state that is reached due to the dynamic change may not be a safe configuration for this system. For instance, it is possible that two processors that became neighbors due to the link addition have the same color. Clearly, a safe configuration is reached when each processor follows the algorithm presented in figure 7.1. However, it is possible that, during the convergence, almost every processor will change its color.

假设在两个处理器之间添加了一条链路或向系统添加了一个新的处理器。注意，我们假设上述添加不会违反每个处理器的邻居数量不超过 $\triangle$ 的限制。由于动态变化所达到的新系统状态可能不是这个系统的安全配置。例如，由于链路添加而成为邻居的两个处理器可能具有相同的颜色。显然，当每个处理器遵循图 7.1 中提出的算法时，会达到一个安全配置。然而，在收敛过程中，几乎每个处理器都有可能改变其颜色。

For example, consider a system in the form of a chain $P_1, P_2, ···, P_n$ where, for every $1 \leq i \leq n - 1$, $P_i$ is connected to $P_{i+1}$, and where the link between $P_{n-1}$ and $P_n$ is inoperative. If the link between $P_{n-1}$ and $P_n$ recovers while ${color}_n = {color}_{n-1}$, it is then possible that, during the execution of the algorithm in figure 7.1, every processor will change its color. Such a scenario can be avoided by using the topology change as a signal for $P_n$ and $P_{n-1}$ to change colors in a careful way. Figure 7.2 presents a superstabilizing algorithm for the coloring task. Each processor repeatedly reads the color of its neighbors and uses the variables $AColors$ and $GColors$ to store the set of colors it finds. In particular, $AColors$ stores the set of colors of all the neighbors and $GColors$ stores the set of colors of the neighbors that have a greater identifier (lines 2 through 8 of the code).

例如，考虑一个链形式的系统 $P_1, P_2, ···, P_n$，其中，对于每个 $1 \leq i \leq n - 1$，$P_i$ 连接到 $P_{i+1}$，且 $P_{n-1}$ 和 $P_n$ 之间的链路不可用。如果 $P_{n-1}$ 和 $P_n$ 之间的链路在 ${color}_n = {color}_{n-1}$ 的情况下恢复，那么在执行图 7.1 中的算法时，每个处理器都有可能改变其颜色。通过将拓扑变化作为 $P_n$ 和 $P_{n-1}$ 谨慎改变颜色的信号，可以避免这种情况。图 7.2 展示了一个用于着色任务的超稳定算法。每个处理器反复读取其邻居的颜色，并使用变量 $AColors$ 和 $GColors$ 存储它发现的颜色集合。特别地，$AColors$ 存储所有邻居的颜色集合，$GColors$ 则存储标识符较大的邻居的颜色集合（代码的第 2 至第 8 行）。

![figure_7.2](images/figure_7.2.png)

A special symbol $\perp$ is used by the superstabilizing algorithm to flag a nonexistent color. When a link connecting the processors $P_i$ and $P_j$ recovers, both $P_i$ and $P_j$ receive an interrupt signal, ${recover}_{ij}$ and ${recover}_{ji}$, respectively. Assume, without loss of generality, that $j > i$; in such a case, the interrupt ${recover}_{ij}$ triggers an assignment ${color}_i := \perp$ (lines 14 and 15 of the code). In our example $P_{n-1}$ assigns $\perp$ to ${color}_{n-1}$ and $r_{n-1}.color$. Then $P_{n-1}$ chooses a color different from the color of $P_n$ and $P_{n-2}$. Thus, a safe configuration is reached. To prove superstabilization of an algorithm, one needs first to prove that the algorithm is self-stabilizing. Consider an execution of the superstabilizing algorithm in figure 7.2 in which no topology change occurs. In every configuration that follows the first asynchronous cycle of the algorithm, the values of ${color}_i$ and $r_i.color$ of every processor $P_i$ are not equal to $\perp$. Moreover, processors execute only lines 1 to 12 of the code during the execution. Thus, following the first asynchronous cycle, the processor $P_n$ with the largest identifier has a fixed color. The proof continues by induction, proving that the processor $P_{n-1}$ with the second largest identifier chooses a color that is fixed through the entire execution during the next cycle. The proof uses the fact that the color of $P_n$ is already fixed, so that when $P_{n-1}$ is a neighbor of $P_n$, $P_{n-1}$ does not choose the color of $P_n$. The same argument is used repeatedly to prove that $P_i$ chooses a color that is different from the color of every neighbor $P_j$ with $j > i$ in the $j$ th cycle, and that this color remains fixed.

超稳定算法使用特殊符号 $\perp$ 来标记不存在的颜色。当连接处理器 $P_i$ 和 $P_j$ 的链路恢复时，$P_i$ 和 $P_j$ 分别接收到一个中断信号，${recover}_{ij}$ 和 ${recover}_{ji}$。假设，无损失地，$j > i$；在这种情况下，中断 ${recover}_{ij}$ 触发赋值 ${color}_i := \perp$（代码的第 14 和 15 行）。在我们的示例中，$P_{n-1}$ 将 $\perp$ 赋值给 ${color}_{n-1}$ 和 $r_{n-1}.color$。然后，$P_{n-1}$ 选择一个不同于 $P_n$ 和 $P_{n-2}$ 颜色的颜色。这样，就达到了一个安全配置。为了证明算法的超稳定性，首先需要证明算法是自稳定的。考虑图 7.2 中的超稳定算法的一次执行，其中没有发生拓扑变化。在算法的第一次异步循环后的每个配置中，每个处理器 $P_i$ 的 ${color}_i$ 和 $r_i.color$ 的值都不等于 $\perp$。此外，在执行过程中，处理器只执行代码的第 1 到第 12 行。因此，在第一次异步循环后，标识符最大的处理器 $P_n$ 具有固定颜色。证明通过归纳法继续，证明标识符第二大的处理器 $P_{n-1}$ 在下一个循环中选择一个在整个执行过程中固定的颜色。证明利用了 $P_n$ 的颜色已经固定的事实，因此当 $P_{n-1}$ 是 $P_n$ 的邻居时，$P_{n-1}$不会选择$P_n$的颜色。同样的论点被重复使用，证明 $P_i$ 在第 $j$ 个循环中选择的颜色不同于每个标识符大于 $i$ 的邻居处理器 $P_j$ 的颜色，并且这种颜色保持固定。

The terms *passage predicate*, *superstabilizing time complexity*, and *adjustment measure* can be demonstrated using the superstabilizing coloring algorithm in figure 7.2. The passage predicate ensures that the color of neighboring processors is always different in any execution that starts in a safe configuration and in which only a single edge may fail or recover before the next safe configuration is reached. Superstabilizing time complexity is measured by the number of cycles required to reach a safe configuration following a topology change. Clearly, the superstabilizing time complexity of the algorithm in figure 7.2 is one cycle (while the stabilizing complexity is $O(n)$ cycles). The adjustment measure for our algorithm is the number of processors that change color upon a topology change; this is only one processor, when the topology changes considered are link addition or removal.

使用图 7.2 中的超稳定着色算法可以展示 *通行谓词*、*超稳定时间复杂度* 和 *调整量* 术语。通行谓词确保在任何从安全配置开始的执行中，相邻处理器的颜色总是不同，并且只有一条边可能会失效或恢复，然后再达到下一个安全配置。超稳定时间复杂度通过拓扑变化后达到安全配置所需的循环次数来衡量。显然，图 7.2 中算法的超稳定时间复杂度是一个循环（而稳定时间复杂度是 $O(n)$ 循环）。我们算法的调整量是拓扑变化后改变颜色的处理器数量；当考虑的拓扑变化是链路添加或删除时，这只有一个处理器。
