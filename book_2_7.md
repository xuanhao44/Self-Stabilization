# 2.7 Fair Composition of Self-Stabilizing Algorithms

Several techniques have been proposed for designing, analyzing, and proving the correctness of self-stabilizing algorithms. In this section, we introduce the technique of fair composition of self-stabilizing algorithms. The idea is to compose self-stabilizing algorithms $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_k$ so that the stabilized behavior (roughly speaking, output) of $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_i$ is used (as an input) by $\mathcal{AL}_{i+1}$. The algorithms are executed in a fair fashion—each processor executes a step of each algorithm infinitely often. **$\mathcal{AL}_{i+1}$ cannot detect whether the algorithms $\mathcal{AL}_1$ to $\mathcal{AL}_i$ have stabilized, but $\mathcal{AL}_{i+1}$ is executed as if these algorithms $\mathcal{AL}_1$ to $\mathcal{AL}_i$ have done so**. Thus, when every algorithm up to the $i$ th algorithm has stabilized, $\mathcal{AL}_{i+1}$ is in an arbitrary state from which it starts converging to a legal behavior.

已经提出了几种设计、分析和证明自稳定算法正确性的方法。在本节中，我们介绍了自稳定算法的公平组合技术。其思想是将自稳定算法 $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_k$ 组合起来，使得 $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_i$ 的稳定行为（粗略地说，输出）被 $\mathcal{AL}_{i+1}$ 用作输入。这些算法以公平的方式执行——每个处理器无限次地执行每个算法的一步。**$\mathcal{AL}_{i+1}$ 无法检测 $\mathcal{AL}_1$ 到 $\mathcal{AL}_i$ 是否已经稳定，但 $\mathcal{AL}_{i+1}$ 的执行方式就像这些算法 $\mathcal{AL}_1$ 到 $\mathcal{AL}_i$ 已经稳定一样**。因此，当每个算法直到第 $i$ 个算法都已稳定时，$\mathcal{AL}_{i+1}$ 处于一个任意状态，从该状态开始收敛到合法行为。

The technique is described for $k = 2$ (i.e., composition of two self-stabilizing algorithms), and the generalization to $k > 2$ is obvious. Two simple algorithms, called a *server* algorithm and a *client* algorithm, are combined to obtain a more complex algorithm. **The server algorithm ensures that some properties will hold, and these properties are later used by the client algorithm.**

该技术针对 $k = 2$（即两个自稳定算法的组合）进行了描述，并且推广到 $k > 2$ 是显而易见的。两个简单的算法，称为 *服务器* 算法和 *客户端* 算法，被组合以获得一个更复杂的算法。**服务器算法确保某些属性将保持，这些属性随后被客户端算法使用。**

Assume that the server algorithm $\mathcal{AL}_1$ is for a task defined by the set of legal executions $\mathcal{T}_1$, and that the client algorithm $\mathcal{AL}_2$ is for a task defined by the set of legal executions $\mathcal{T}_2$. A processor $P_i$ runs the two algorithms $\mathcal{AL}_1$ and $\mathcal{AL}_2$ in alternation, one step of $\mathcal{AL}_1$ and then a step of $\mathcal{AL}_2$, and so on.

Let $A_i$ be the state set of $P_i$ in $\mathcal{AL}_1$ and $S_i = A_i × B_i$ the state set of $P_i$ in $\mathcal{AL}_2$, where, whenever $P_i$ executes $\mathcal{AL}_2$, it modifies only the $B_i$ components of $A_i×B_i$ (for simplicity we assume that the communication registers in which a processor writes are parts of the processor state). **In other words, whenever $P_i$ executes a step in $\mathcal{AL}_1$, it uses its state in $\mathcal{AL}_1$, ignoring the portion of the state that is modified in the steps of $\mathcal{AL}_2$. Whenever $P_i$ executes a step in $\mathcal{AL}_2$, it uses its state in $\mathcal{AL}_1$ and $\mathcal{AL}_2$ to compute its new $B_i$ portion of its state in $\mathcal{AL}_2$.**

假设服务器算法 $\mathcal{AL}_1$ 是为由合法执行集 $\mathcal{T}_1$ 定义的任务设计的，而客户端算法 $\mathcal{AL}_2$ 是为由合法执行集 $\mathcal{T}_2$ 定义的任务设计的。处理器 $P_i$ 交替运行两个算法 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$，即 $\mathcal{AL}_1$ 的一步，然后是 $\mathcal{AL}_2$ 的一步，依此类推。

设 $A_i$ 是 $P_i$ 在 $\mathcal{AL}_1$ 中的状态集，$S_i = A_i × B_i$ 是 $P_i$ 在 $\mathcal{AL}_2$ 中的状态集，其中，每当 $P_i$ 执行 $\mathcal{AL}_2$ 时，它只修改 $A_i×B_i$ 的 $B_i$ 组件（为简化起见，我们假设处理器写入的通信寄存器是处理器状态的一部分）。**换句话说，每当 $P_i$ 在 $\mathcal{AL}_1$ 中执行一步时，它使用其在 $\mathcal{AL}_1$ 中的状态，忽略在 $\mathcal{AL}_2$ 的步骤中修改的状态部分。每当 $P_i$ 在 $\mathcal{AL}_2$ 中执行一步时，它使用其在 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 中的状态来计算其在 $\mathcal{AL}_2$ 中的新 $B_i$ 状态部分。**

The following definitions formalize the concept of an execution of the client algorithm, which assumes self-stabilized execution of the server algorithm. Let $S_i$, $A_i$, $B_i$, $\mathcal{AL}_2$ and $\mathcal{T}_1$ be as above, where $\mathcal{T}_1$ is defined using the $A_i$ portion of the states of every processor $P_i$ . For a configuration $c$, $c ∈ S_1×...×S_n$, define the *A-projection* of $c$ as the configuration $({ap}_1, ..., {ap}_n) ∈ A_1 × ... × A_n$. The *A-projection* of an execution is defined analogously to consist of the *A-projection* of every configuration of the execution.

以下定义形式化了客户端算法执行的概念，该算法假设服务器算法的自稳定执行。设 $S_i$、$A_i$、$B_i$、$\mathcal{AL}_2$ 和 $\mathcal{T}_1$ 如上，其中 $\mathcal{T}_1$ 是使用每个处理器 $P_i$ 的状态的 $A_i$ 部分定义的。对于配置 $c$，$c ∈ S_1×...×S_n$，定义 $c$ 的 *A-投影* 为配置 $({ap}_1, ..., {ap}_n) ∈ A_1 × ... × A_n$。执行的 *A-投影* 类似地定义为执行的每个配置的 *A-投影*。

We say that *algorithm $\mathcal{AL}_2$ is self-stabilizing for task $\mathcal{T}_2$ given task  $\mathcal{T}_1$* **if any fair execution of $\mathcal{AL}_2$ that has an *A-projection* in $\mathcal{T}_1$ has a suffix in $\mathcal{T}_2$**. Finally, an algorithm $\mathcal{AL}$ is a *fair composition* of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ if, in $\mathcal{AL}$, every processor execute steps of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ alternately. In fact it is sufficient that every processor executes steps of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ infinitely often. Note that, for an execution $E$ of $\mathcal{AL}$, the *A-projection* of $E$ is a sub-execution of E corresponding to a fair execution of the server algorithm $\mathcal{AL}_1$.

我们说 *算法 $\mathcal{AL}_2$ 在任务 $\mathcal{T}_1$ 给定的情况下是自稳定的，用于任务 $\mathcal{T}_2$*，**如果 $\mathcal{AL}_2$ 的任何公平执行在 $\mathcal{T}_1$ 中有一个 *A-投影*，则它在 $\mathcal{T}_2$ 中有一个后缀**。最后，如果在算法 $\mathcal{AL}$ 中，每个处理器交替执行 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的步骤，那么算法 $\mathcal{AL}$ 是 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的 *公平组合*。实际上，只要每个处理器无限次地执行 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的步骤就足够了。注意，对于 $\mathcal{AL}$ 的执行 $E$，$E$ 的 *A-投影* 是 $E$ 的一个子执行，对应于服务器算法 $\mathcal{AL}_1$ 的公平执行。

The following theorem gives sufficient conditions under which the composition of two self-stabilizing algorithms is also self-stabilizing.

以下定理给出了两个自稳定算法的组合也是自稳定的充分条件。

---

> THEOREM 2.2: Assume that $\mathcal{AL}_2$ is self-stabilizing for a task $\mathcal{T}_2$ given task $\mathcal{T}_1$ If $\mathcal{AL}_1$ is self-stabilizing for $\mathcal{T}_1$, then the fair composition of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ is self-stabilizing for $\mathcal{T}_2$.
>
> 定理 2.2：假设 $\mathcal{AL}_2$ 在任务 $\mathcal{T}_1$ 给定的情况下是自稳定的，用于任务 $\mathcal{T}_2$。如果 $\mathcal{AL}_1$ 是 $\mathcal{T}_1$ 的自稳定算法，那么 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合是 $\mathcal{T}_2$ 的自稳定算法。

*Proof:*

Consider any execution $E$ of $\mathcal{AL}$, the fair composition of $\mathcal{AL}_1$ and $\mathcal{AL}_2$. By the self-stabilization of $\mathcal{AL}_1$, $E$ has a suffix $E'$ such that the *A-projection* of $E'$ is in $\mathcal{T}_1$. By the assumption that $\mathcal{AL}_2$ is self-stabilizing given $\mathcal{T}_1$, $E$ has a suffix in $\mathcal{T}_2$. (End)

*证明：*

考虑 $\mathcal{AL}$ 的任何执行 $E$，即 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合。根据 $\mathcal{AL}_1$ 的自稳定性，$E$ 有一个后缀 $E'$，使得 $E'$ 的 *A-投影* 在 $\mathcal{T}_1$ 中。根据假设 $\mathcal{AL}_2$ 在 $\mathcal{T}_1$ 给定的情况下是自稳定的，$E$ 在 $\mathcal{T}_2$ 中有一个后缀。（完）

---

Theorem 2.2 provides a general methodology for constructing self-stabilizing algorithms for complex tasks.

- Given a task $\mathcal{T}_2$ for which we wish to construct such an algorithm, first define a task  $\mathcal{T}_1$ and construct an algorithm $\mathcal{AL}_2$ that is self-stabilizing for $\mathcal{T}_2$ given $\mathcal{T}_1$,
- and then construct an algorithm $\mathcal{AL}_1$ that is self-stabilizing for $\mathcal{T}_1$. The fair composition of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ is the desired algorithm. Note that this methodology does not require that the algorithm $\mathcal{AL}_1$ reach a “steady state” in which the communication registers (or any other component in the state ${ap}_i$ of processor $P_i$) are never changed.

Moreover, the fair-composition methodology can be used for message-passing systems. For example, $\mathcal{AL}_1$ can be a self-stabilizing data-link algorithm that uses retransmissions to guarantee that, from some point on, every message sent from one processor to its neighbor reaches its destination. $\mathcal{AL}_2$ can be a self-stabilizing end-to-end algorithm that guarantees that, from some point on, every message sent from one processor in the network to another processor reaches its destination under the assumption that the data-link service is reliable. Clearly $\mathcal{AL}_2$ will not operate correctly until $\mathcal{AL}_1$ is stabilized and operating as it should. Eventually $\mathcal{AL}_1$ reaches a safe configuration with relation to $\mathcal{T}_1$; in this configuration, the $B_i$ components of the state of $P_i$ are arbitrary. Fortunately, $\mathcal{AL}_2$ is self-stabilizing when $\mathcal{AL}_1$ is already stabilized. Therefore a safe configuration for $\mathcal{AL}_2$ with relation to $\mathcal{T}_2$ is reached.

**定理 2.2 提供了一种构建复杂任务的自稳定算法的一般方法。**

- **给定一个任务 $\mathcal{T}_2$，我们希望为其构建这样的算法，首先定义一个任务 $\mathcal{T}_1$ 并构建一个在 $\mathcal{T}_1$ 给定的情况下对 $\mathcal{T}_2$ 自稳定的算法 $\mathcal{AL}_2$，**
- **然后构建一个对 $\mathcal{T}_1$ 自稳定的算法 $\mathcal{AL}_1$。$\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合就是所需的算法。注意，这种方法不要求算法 $\mathcal{AL}_1$ 达到“稳定状态”，即通信寄存器（或处理器 $P_i$ 状态中的任何其他组件）从未改变。**

此外，公平组合方法可以用于消息传递系统。例如，$\mathcal{AL}_1$ 可以是一个自稳定的数据链路算法，该算法使用重传来保证从某个时刻起，从一个处理器发送到其邻居的每条消息都能到达目的地。$\mathcal{AL}_2$ 可以是一个自稳定的端到端算法，在假设数据链路服务可靠的情况下，保证从某个时刻起，从网络中的一个处理器发送到另一个处理器的每条消息都能到达目的地。显然，$\mathcal{AL}_2$ 在 $\mathcal{AL}_1$ 稳定并正常运行之前不会正确运行。最终，$\mathcal{AL}_1$ 达到与 $\mathcal{T}_1$ 相关的安全配置；在这种配置中，$P_i$ 的状态的 $B_i$ 组件是任意的。幸运的是，当 $\mathcal{AL}_1$ 已经稳定时，$\mathcal{AL}_2$ 是自稳定的。因此，$\mathcal{AL}_2$ 达到了与 $\mathcal{T}_2$ 相关的安全配置。

## Example: Mutual Exclusion for General Communication Graphs

To demonstrate the power of the fair-composition method, let us compose the spanning-tree construction algorithm of section 2.5 with the mutual exclusion algorithm presented in section 2.6. The combination of these algorithms is a self-stabilizing algorithm for mutual exclusion in systems with general communication graphs. The spanning-tree construction is the server algorithm and the mutual exclusion is the client algorithm.

为了展示公平组合方法的强大功能，让我们将第 2.5 节的生成树构造算法与第 2.6 节介绍的互斥算法组合起来。这些算法的组合是一个在具有一般通信图的系统中用于互斥的自稳定算法。生成树构造是服务器算法，互斥是客户端算法。

The mutual exclusion algorithm presented in figure 2.2 is designed for a system in the shape of a ring, assuming that a powerful scheduler schedules the steps of the processors. Note that a slight modification of the algorithm can be applied to systems that support only read/write atomicity as presented in section 2.6. In figure 2.5 we present a version of the mutual exclusion algorithm of figure 2.2 that is designed to stabilize in a system in which a rooted spanning tree exists and in which only read/write atomicity is assumed.

图 2.2 中介绍的互斥算法是为环形系统设计的，假设一个强大的调度器调度处理器的步骤。请注意，该算法的稍微修改版本可以应用于仅支持读/写原子性的系统，如第 2.6 节所述。在图 2.5 中，我们展示了图 2.2 中互斥算法的一个版本，该版本旨在稳定在存在根生成树并且仅假设读/写原子性的系统中。

![figure_2.4](images/figure_2.4.png)

One key observation used to apply the mutual exclusion algorithm to a rooted spanning-tree system is the fact that a Euler tour on a spanning tree defines a (virtual) ring. For example, consider a rooted tree of four processors, where $P_1$ is the root and $P_4$, $P_2$, and $P_3 $are $P_1$’s children, ordered according to an arbitrary but fixed order chosen by $P_1$. In this case, the sequence ${lx}_1, x_1, {lx}_2, x_2, · · ·$ presented in section 2.6 corresponds to ${lr}_{4,1}, r_{1,2}, {lr}_{1,2}, r_{2,1}, {lr}_{2,1}, r_{1,3}, {lr}_{1,3}, r_{3,1}, {lr}_{3,1}, r_{1,4}, {lr}_{1,4}, r_{4,1}$. Figure 2.4 depicts the virtual ring embedded in the above rooted tree, where $P_1$ acts as three processors in the ring (one for each attached link). In particular, $P_1$ acts as the special processor using ${lr}_{4,1}$ and $r_{1,2}$ — whenever ${lr}_{4,1}$ equals $r_{1,2}$, $P_1$ increments the value in $r_{1,2}$ modulo $4n − 5$.

应用互斥算法到根生成树系统的一个关键观察是，生成树上的欧拉巡游定义了一个（虚拟）环。例如，考虑一个有四个处理器的根树，其中 $P_1$ 是根，$P_4$、$P_2$ 和 $P_3$ 是 $P_1$ 的子节点，按照 $P_1$ 选择的任意但固定的顺序排列。在这种情况下，第 2.6 节中提出的序列 ${lx}_1, x_1, {lx}_2, x_2, · · ·$ 对应于 ${lr}_{4,1}, r_{1,2}, {lr}_{1,2}, r_{2,1}, {lr}_{2,1}, r_{1,3}, {lr}_{1,3}, r_{3,1}, {lr}_{3,1}, r_{1,4}, {lr}_{1,4}, r_{4,1}$。图 2.4 描绘了嵌入上述根树中的虚拟环，其中 $P_1$ 在环中充当三个处理器（每个连接的链路一个）。特别地，$P_1$ 作为使用 ${lr}_{4,1}$ 和 $r_{1,2}$ 的特殊处理器——每当 ${lr}_{4,1}$ 等于 $r_{1,2}$ 时，$P_1$ 将 $r_{1,2}$ 的值按模 $4n − 5$ 增加。

In general, let $α_i = i_1, i_2, · · · , i_δ$ be the arbitrary ordering of the tree edges incident to a non-root node $v_i ∈ V$, where the first edge is the edge leading to the parent of vi in the tree. For the sake of readability let us use the index $j$ for $i_j$, resulting in $α_i = 1, 2, · · · , δ$. Pi repeatedly executes steps in which $P_i$ reads the register $r_{1,i}$ into ${lr}_{1,i}$, then writes the value of ${lr}_{1,i}$ into $r_{i,2}$, and then reads the register ${lr}_{2,i}$ and writes to $r_{i,3}$, and so on, until it writes the value of ${lr}_{δ,i}$ to $r_{i,1}$. The above read/write pattern is repeated. Roughly speaking, $P_i$ plays the role of $δ$ processors in the (virtual) ring. To avoid multiple entries to the critical section, each emulated processor $P_i$ enters the critical section only when the value of ${lr}_{1,i}$ is not equal to the value of $r_{i,2}$. In contrast, the root processor (i.e., the special processor) enters the critical section whenever the value of ${lr}_{δ,i}$ is equal to the value of $r_{i,1}$. The mutual-exclusion algorithm for a spanning-tree system appears in figure 2.5. The indices of $r_{i,m+1}$ are computed modulo $δ$.

一般来说，设 $α_i = i_1, i_2, · · · , i_δ$ 为与非根节点 $v_i ∈ V$ 相邻的树边的任意顺序，其中第一条边是通向树中 $v_i$ 父节点的边。为了便于阅读，我们使用索引 $j$ 表示 $i_j$，结果是 $α_i = 1, 2, · · · , δ$。$P_i$ 重复执行以下步骤：$P_i$ 将寄存器 $r_{1,i}$ 读入 ${lr}_{1,i}$，然后将 ${lr}_{1,i}$ 的值写入 $r_{i,2}$，然后读取寄存器 ${lr}_{2,i}$ 并写入 $r_{i,3}$，依此类推，直到将 ${lr}_{δ,i}$ 的值写入 $r_{i,1}$。上述读/写模式重复进行。粗略地说，$P_i$ 在（虚拟）环中扮演 $δ$ 个处理器的角色。为了避免多次进入临界区，每个模拟处理器 $P_i$ 只有在 ${lr}_{1,i}$ 的值不等于 $r_{i,2}$ 的值时才进入临界区。相比之下，根处理器（即特殊处理器）每当 ${lr}_{δ,i}$ 的值等于 $r_{i,1}$ 的值时进入临界区。生成树系统的互斥算法如图 2.5 所示。$r_{i,m+1}$ 的索引按模 $δ$ 计算。

![figure_2.5](images/figure_2.5.png)

The mutual-exclusion algorithm can be applied to a spanning tree of the system using the ring defined by the Euler tour on the tree. However, the mutual exclusion algorithm cannot be applied to a system with a general graph in which a spanning tree is not defined. Fortunately, the value of the parent fields of the server algorithm eventually defines the parent of each processor in the tree, and hence defines the children of each processor as well.

互斥算法可以应用于系统的生成树，使用树上的欧拉巡游定义的环。然而，互斥算法不能应用于没有定义生成树的一般图系统。幸运的是，服务器算法的父字段的值最终定义了树中每个处理器的父节点，从而也定义了每个处理器的子节点。

In the composition of the algorithms, each processor alternately executes a step of each of the composed algorithms. Let us consider a particular way to implement such a composition, where an internal binary variable called ${turn}_i$ is used by each processor $P_i$ to indicate which of the algorithms is to be executed next. $P_i$ maintains a program counter for each of the algorithms. Whenever $P_i$ is scheduled to execute a step and ${turn}_i = 0$ (${turn}_i = 1$, respectively), $P_i$ executes the next step of the spanning-tree construction (the mutual exclusion, respectively) algorithm and assigns ${turn}_i := 1−{turn}_i$. Each processor executes an atomic step infinitely often in every fair execution. This fact, to gether with the use of the ${turn}_i$ variable, ensures that every processor executes an atomic step of each of the composed algorithms infinitely often.

在算法的组合中，每个处理器交替执行每个组合算法的一步。让我们考虑一种实现这种组合的特定方法，其中每个处理器 $P_i$ 使用一个内部二进制变量 ${turn}_i$ 来指示下一个要执行的算法。$P_i$ 为每个算法维护一个程序计数器。每当 $P_i$ 被调度执行一步且 ${turn}_i = 0$（分别为 ${turn}_i = 1$）时，$P_i$ 执行生成树构造算法（分别为互斥算法）的下一步，并将 ${turn}_i := 1−{turn}_i$。每个处理器在每次公平执行中无限次地执行一个原子步骤。这个事实，加上使用 ${turn}_i$ 变量，确保每个处理器无限次地执行每个组合算法的原子步骤。

The fact that each processor executes the spanning-tree construction infinitely often implies that, at some point of the execution, a fixed spanning tree is defined by the value of the distance fields of the communication registers. When a processor executes the mutual-exclusion algorithm, it uses the value of the parent fields of the tree-construction algorithm for the definition of the tree links that are attached to it. Therefore, when the server algorithm is not yet stabilized the mutual-exclusion algorithm may be executed over a non-tree structure. Hence, it is possible that the execution of the mutual-exclusion algorithm, during the convergence period of the tree construction algorithm, is not an execution that was considered when the self-stabilizing mutual-exclusion algorithm was designed. Consequently, once the tree is fixed, the self-stabilizing mutual-exclusion algorithm is in an arbitrary state from which it converges to reach a safe configuration.

每个处理器无限次地执行生成树构造算法这一事实意味着，在执行的某个时刻，通信寄存器的距离字段的值定义了一个固定的生成树。当处理器执行互斥算法时，它使用树构造算法的父字段的值来定义附加到它的树链接。因此，当服务器算法尚未稳定时，互斥算法可能在非树结构上执行。因此，在树构造算法的收敛期间，互斥算法的执行可能不是设计自稳定互斥算法时考虑的执行。因此，一旦树固定，自稳定互斥算法处于一个任意状态，从该状态开始收敛以达到安全配置。

A different example, demonstrating the applicability of the fair composition technique for the case in which the output of the server is not fixed, is a server algorithm that implements a powerful scheduler— the central daemon. The composition of the previous two algorithms ensures that exactly one processor is executing the critical section and each processor executes the critical section infinitely often. Therefore the composed algorithm can implement a powerful scheduler that chooses one processor at a time to be activated. Again, it is possible that, during the convergence of the server algorithm, several processors are activated simultaneously. However, once the server algorithm stabilizes, only a single processor is activated at a time, ensuring the stabilization of a client algorithm that stabilizes (only) in the presence of such a powerful scheduler (more details are given in section 4.1).

另一个例子，展示了公平组合技术在服务器输出不固定情况下的适用性，是一个实现强大调度器——中央守护进程的服务器算法。前面两个算法的组合确保只有一个处理器在执行临界区，并且每个处理器无限次地执行临界区。因此，组合算法可以实现一个强大的调度器，每次选择一个处理器进行激活。同样，在服务器算法的收敛期间，可能会有多个处理器同时被激活。然而，一旦服务器算法稳定，每次只激活一个处理器，从而确保客户端算法在这种强大调度器存在的情况下稳定（更多细节见第 4.1 节）。
