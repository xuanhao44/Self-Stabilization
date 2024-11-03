# 2.5 Example: Spanning-Tree Construction

例子：生成树构造

To demonstrate the use of our definition and requirements, we present a simple self-stabilizing algorithm for marking a breadth-first search (*BFS*) spanning tree over the communication graph of the distributed system $G(V, E)$. Each node $v_i \in V$ represents the processor $P_i$, and each edge $(v_i, v_j) \in E$ indicates that $P_i$ and $P_j$ are neighbors; i.e., they can communicate with each other. For this example we use the shared memory model. A processor $P_i$ communicates with its neighbor $P_j$ by writing in the communication register $r_{ij}$ and reading from $r_{ji}$. A processor $P_i$ owns the register in which $P_i$ writes; i.e., for every neighboring processor $P_k$, the register $r_{ik}$ is owned by $P_i$.

为了展示我们定义和要求的使用，我们提出了一个简单的自稳定算法，用于标记分布式系统 $G(V, E)$ 的通信图上的广度优先搜索（*BFS*）生成树。每个节点 $v_i \in V$ 代表处理器 $P_i$，每条边 $(v_i, v_j) \in E$ 表示 $P_i$ 和 $P_j$ 是邻居；即它们可以相互通信。在这个例子中，我们使用共享内存模型。处理器 $P_i$ 通过在通信寄存器 $r_{ij}$ 中写入和从 $r_{ji}$ 中读取来与其邻居 $P_j$ 通信。处理器 $P_i$ 拥有 $P_i$ 写入的寄存器；即对于每个邻近的处理器 $P_k$，寄存器 $r_{ik}$ 由 $P_i$ 拥有。

The system consists of $n$ processors $P_1, P_2, ···, P_n$, where $P_2, ···, P_n$ run similar programs while $P_1$ is a special processor that runs a different program. $P_1$ is called the *root* processor of the tree. There is a single program that every non-root processor runs. The program has an input parameter that is the number of adjacent links of the processor. Thus, all two (non-root) processors with the same number of neighbors are identical — they run identical programs.

系统由 $n$ 个处理器 $P_1, P_2, ···, P_n$ 组成，其中 $P_2, ···, P_n$ 运行相似的程序，而 $P_1$ 是一个特殊的处理器，运行不同的程序。$P_1$ 被称为树的 *根* 处理器。每个非根处理器运行一个相同的程序。该程序有一个输入参数，即处理器的相邻链接数。因此，所有具有相同邻居数量的两个（非根）处理器是相同的——它们运行相同的程序。

Essentially **the algorithm is a distributed *BFS* algorithm**. Each processor is continuously trying to compute its distance from the root and to report this distance to all its neighbors by writing the distance in its registers. At the beginning of an arbitrary execution, the only processor guaranteed to compute the right distance is the root itself. Once this distance is written in all the root's registers, the value stored in these registers will never be changed. **Once all processors at distance $x$ from the root have completed computing their distance from the root correctly and have written it in all their registers, their registers remain constant throughout execution**, and processors at distance $x+1$ from the root are ready to compute their own distance from the root, and so forth.

本质上，该算法是一个**分布式的 *BFS* 算法**。每个处理器不断尝试计算其与根的距离，并通过将距离写入其寄存器来将此距离报告给所有邻居。在任意执行的开始，唯一保证能计算出正确距离的处理器是根处理器本身。**一旦这个距离被写入所有根处理器的寄存器，这些寄存器中存储的值将永远不会改变。一旦所有距离根为 $x$ 的处理器完成了与根的距离计算并将其写入所有寄存器，它们的寄存器在整个执行过程中将保持不变**，距离根为 $x+1$ 的处理器将准备好计算它们与根的距离，依此类推。

The output tree is encoded by means of the registers as follows: each register $r_{ij}$, in which $P_i$ writes and from which $P_j$ reads, contains a binary parent field denoted by $r_{ij}.parent$. If $P_j$ is the parent of $P_i$ in the *BFS* tree, then the value of $r_{ij}.parent$ is 1; otherwise the value of $r_{ij}.parent$ is 0. In addition, each register $r_{ij}$ has a $distance$ field, denoted by $r_{ij}.dis$, that holds the distance from the root to $P_i$. The maximal value that can be stored in the $distance$ field is $N$, where $N$ is an upper bound on the number of processors in the system. An attempt to assign a value larger than $N$ to the $distance$ field results in the assignment of $N$.

输出树通过寄存器进行编码，如下所示：每个寄存器 $r_{ij}$，其中 $P_i$ 写入并且 $P_j$ 读取，包含一个二进制的父字段，表示为 $r_{ij}.parent$。如果 $P_j$ 是 $P_i$ 在 *BFS* 树中的父节点，那么 $r_{ij}.parent$ 的值为 1；否则 $r_{ij}.parent$ 的值为 0。此外，每个寄存器 $r_{ij}$ 还有一个 $distance$ 字段，表示为 $r_{ij}.dis$，它保存从根到 $P_i$ 的距离。$distance$ 字段中可以存储的最大值是 $N$，其中 $N$ 是系统中处理器数量的上限。尝试将大于 $N$ 的值赋给 $distance$ 字段将导致该字段被赋值为 $N$。

The code of the algorithm, for the root and for the other processors, appears in figure 2.1. In this code the number of the processor's neighbors is given by the parameter $\delta$.

The program for the root is very simple: it keeps "telling" all its neighbors that it is the root by repeatedly writing the values $<0, 0>$ in all of its registers. The first 0 tells each neighbor that it is not the parent of the root, the second 0 is the distance from the root to itself.

The program for a normal processor consists of a single loop. In this loop, the processor reads all the registers of its neighbors. Processor $P_i$, which has $\delta$ neighbors, keeps $\delta$ internal variables corresponding to the $\delta$ registers from which $P_i$ reads. The local variable corresponding to register $r_{ji}$, ${lr}_{ji}$, stores the last value of $r_{ji}$ read by $P_i$. Its two fields are denoted by ${lr}_{ji}.parent$ and ${lr}_{ji}.dis$, respectively. Once all these registers are read, $P_i$ computes a value for the variable $dist$ that represents $P_i$'s current idea of its distance from the root. The purpose of the boolean variable $FirstFound$ is to make sure that by the end of each pass of the loop each processor has a single parent. The minimum in line 7 is taken over $m$, $1 \leq m \leq \delta$.

算法的代码，如图 2.1 所示，分别针对根处理器和其他处理器。在此代码中，处理器的邻居数量由参数 $\delta$ 给出。

根处理器的程序非常简单：它通过在所有寄存器中反复写入值 $<0, 0>$ 来不断“告诉”所有邻居它是根。第一个 0 告诉每个邻居它不是根的父节点，第二个 0 是根到自身的距离。

普通处理器的程序由一个单一的循环组成。在这个循环中，处理器读取其所有邻居的寄存器。具有 $\delta$ 个邻居的处理器 $P_i$ 保留了对应于 $P_i$ 读取的 $\delta$ 个寄存器的 $\delta$ 个内部变量。对应于寄存器 $r_{ji}$ 的局部变量 ${lr}_{ji}$ 存储了 $P_i$ 最后读取的 $r_{ji}$ 的值。它的两个字段分别表示为 ${lr}_{ji}.parent$ 和 ${lr}_{ji}.dis$。一旦读取了所有这些寄存器，$P_i$ 就会计算一个变量 $dist$ 的值，该值表示 $P_i$ 当前认为的它与根的距离。布尔变量 $FirstFound$ 的目的是确保在每次循环结束时每个处理器都有一个单一的父节点。第 7 行的最小值取自 $m$，$1 \leq m \leq \delta$。

![figure_2.1](images/figure_2.1.png)

In figure 2.1 we use a program to define implicitly the set of states and the transition function of a processor. The state of a processor consists of the value of the program counter and the values of the internal variables: $m$, ${lr}_{ji}$ (for every $1 \leq j \leq \delta$), $FirstFound$, and $dist$.

A computation step of the root processor starts with local computations that update the value of m (increment the value of $m$ by one if $m \leq \delta$, or assign $m := 1$ otherwise). The single communication operation that is executed in a computation step of the root is a write operation of $<0, 0>$ in $r_{im}$ (where the value of $m$ is defined by the preceding local computations). The computation steps end with this write operation. The next computation step starts with the local computation that immediately follows the last write operation, and so on.

Similarly, a computation step of the non-root processor terminates in one of the three communication operations ($\text{read}(r_{mi}), \text{write}(r_{im}) := <1, dis>$ or $\text{write}(r_{im}) := <0, dis>$), and the next computation starts immediately following this communication operation.

One can argue that, in the context of self-stabilization, the value of the program counter can be arbitrary in the first system configuration and not restricted to the first local computation that follows a communication operation. Thus, it is possible that, in line 10 of the code, a $dist$ value that was not computed in line 7 is used. However, following the first computation step every computation step is well structured: it starts immediately following a communication operation and ends with the next communication operation.

在图 2.1 中，我们使用一个程序隐式地定义了处理器的状态集合和转换函数。处理器的状态包括程序计数器的值和内部变量的值：$m$，${lr}_{ji}$（对于每个 $1 \leq j \leq \delta$），$FirstFound$ 和 $dist$。

根处理器的计算步骤从更新 $m$ 的值的本地计算开始（如果 $m \leq \delta$，则将 $m$ 的值加一，否则将 $m$ 赋值为 1）。根处理器在计算步骤中执行的唯一通信操作是在 $r_{im}$ 中写入 $<0, 0>$（其中 $m$ 的值由前面的本地计算定义）。计算步骤以此写操作结束。下一计算步骤从紧接上次写操作后的本地计算开始，依此类推。

类似地，非根处理器的计算步骤在三种通信操作之一结束（$\text{read}(r_{mi})$，$\text{write}(r_{im}) := <1, dis>$ 或 $\text{write}(r_{im}) := <0, dis>$），下一计算步骤紧接着此通信操作开始。

可以认为，在自稳定的上下文中，程序计数器的值在第一个系统配置中可以是任意的，而不局限于通信操作后的第一个本地计算。因此，在代码的第 10 行中，可能会使用第 7 行中未计算的 $dist$ 值。然而，在第一次计算步骤之后，每个计算步骤都是结构良好的：它从通信操作后立即开始，并以下一次通信操作结束。

The value of each communication register is a combination of a binary value (for the $parent$ field) and an integer (no larger than $N$ for the $dis$ field). A configuration of the system is a vector of the processor states and a vector of communication register values.

每个通信寄存器的值是二进制值（用于 $parent$ 字段）和整数（用于 $dis$ 字段，且不大于 $N$）的组合。系统的配置是处理器状态向量和通信寄存器值向量的组合。

**The task $ST$ of legitimate sequences is defined as the set of all configuration sequences in which every configuration encodes a *BFS* tree of the communication graph. In fact, a particular *BFS* tree called the *first BFS tree* is encoded.** Let $\alpha = (\alpha_1, \alpha_2, ...\alpha_n)$ be the arbitrary ordering of the edges incident to each node $v_i \in V$. The first *BFS* tree of a communication graph $G$ is uniquely defined by the choice of the root $v_1$ and $\alpha$. When a node $v_i$ of distance $x +1$ from $v_1$ has more than a single neighbor of distance $x$ from $v_1$, **$v_i$ is connected to its first neighbor according to $\alpha_i$**, whose distance from $v_1$ is $x$. In the lemma below, we use the definition of the first *BFS* tree to characterize the set of safe configurations for the algorithm.

**合法序列任务 $ST$ 定义为所有配置序列的集合，其中每个配置都编码了通信图的一个 *BFS* 树。实际上，编码的是一个特定的 *BFS* 树，称为 *第一个 BFS 树*。**设 $\alpha = (\alpha_1, \alpha_2, ...\alpha_n)$ 为每个节点 $v_i \in V$ 的边的任意排序。通信图 $G$ 的 *第一个 BFS 树* 由根 $v_1$ 和 $\alpha$ 唯一定义。当距离 $v_1$ 为 $x+1$ 的节点 $v_i$ 有多个距离 $v_1$ 为 $x$ 的邻居时，**$v_i$ 根据 $\alpha_i$ 连接到其第一个邻居**，该邻居距离 $v_1$ 为 $x$。在下面的引理中，我们使用第一个 *BFS* 树的定义来描述算法的一组安全配置。

The lemma below shows that, in every execution, a safe configuration is reached. **We use $\triangle$ to denote the maximum number of links adjacent to a processor**, and use the following definitions of *floating distances* and *smallest floating distance* in our proof.

下面的引理表明，在每次执行中，都会达到一个安全配置。**我们用 $\triangle$ 表示与处理器相邻的最大链接数**，并在证明中使用以下 *浮动距离* 和 *最小浮动距离* 的定义。

---

> DEFINITION 2.1: A floating distance in some configuration c is a value in a register $r_{ij}.dis$ that is smaller than the distance of $P_i$ from the root. The smallest floating distance in some configuration $c$ is the smallest value among the floating distances.
>
> 定义 2.1：在某个配置 $c$ 中，浮动距离是寄存器 $r_{ij}.dis$ 中的一个值，该值小于 $P_i$ 到根的距离。在某个配置 $c$ 中，最小浮动距离是浮动距离中的最小值。

---

> LEMMA 2.1: For every $k > 0$ and for every configuration that follows $\triangle+ 4k\triangle$ rounds, it holds that:
>
> *Assertion 1:* If there exists a floating distance, then the value of the smallest floating distance is at least $k$.
>
> *Assertion 2:* The value in the registers of every processor that is within distance $k$ from the root is equal to its distance from the root.
>
> 引理 2.1：对于每个 $k > 0$ 和每个经过 $\triangle+ 4k\triangle$ 轮次后的配置，满足以下条件：
>
> *断言 1：*如果存在浮动距离，那么最小浮动距离的值至少为 $k$。
>
> *断言 2：*每个距离根为 $k$ 以内的处理器的寄存器中的值等于其与根的距离。

*Proof:*

Note that in every $2\triangle$ successive rounds, each processor reads the registers of all its neighbors and writes to each of its registers. We prove the lemma by induction over $k$.

*Base case:*

(proof for $k = 1$)

Distances stored in the registers and internal variables are non-negative; thus the value of the smallest floating distance is at least 0 in the first configuration.

- During the first $2\triangle$ rounds, each non-root processor $P_i$ computes the value of the variable $dist$ (line 7 of the code in figure 2.1). The result of each such computation must be greater than or equal to 1.
- Let $c_2$ be the configuration reached following the first computation of the value of $dist$ by each processor. Each non-root processor writes to each of its registers the computed value of $dist$ during the $2\triangle$ rounds that follow $c_2$.

Thus, in every configuration that follows the first $4\triangle$ rounds there is no non-root processor with value 0 in its registers. The above proves assertion 1.

To prove assertion 2, note that the root repeatedly writes the distance 0 to its registers in every $\triangle$ rounds. Let $c_1$ be the configuration reached after these $\triangle$ rounds.

Each processor reads the registers of the root and then writes to its own registers during the $4\triangle$ rounds that follow $c_1$. In this write operation the processor assigns 1 to its own registers. Any further read of the root registers returns the value 0; therefore, the value of the registers of each neighbor of the root is 1 following the first $\triangle+ 4\triangle$ rounds. Thus, assertion 2 holds as well.

*Induction step:*

(assume correctness for $k \geq 0$ and prove for $k+1$)

Let $m \geq k$ be the smallest floating distance in the configuration $c_{4k}$ that follows the first $\triangle+ 4k\triangle$ rounds. During the $4\triangle$ rounds that follow $c_{4k}$, each processor that reads $m$ and chooses $m$ as the smallest value assigns $m+1$ to its distance and writes this value. Therefore, the smallest floating distance value is $m +1$ in the configuration $c_{4(k+1)}$. This proves assertion 1.

Since the smallest floating distance is $m \geq k$, it is clear that each processor reads the distance of a neighboring processor of distance $k$ and assigns $k+1$ to its distance. (End)

*证明：*

注意，在每 $2\triangle$ 连续轮次中，每个处理器读取其所有邻居的寄存器并写入其每个寄存器。我们通过对 $k$ 进行归纳来证明这个引理。

*基本情况：*

（证明 $k = 1$）

寄存器和内部变量中存储的距离是非负的；因此，在第一个配置中，最小浮动距离的值至少为 0。

- 在前 $2\triangle$ 轮次中，每个非根处理器 $P_i$ 计算变量 $dist$ 的值（图 2.1 中代码的第 7 行）。每次这样的计算结果必须大于或等于 1。
- 设 $c_2$ 为每个处理器首次计算 $dist$ 值后的配置。在 $c_2$ 之后的 $2\triangle$ 轮次中，每个非根处理器将计算出的 $dist$ 值写入其每个寄存器。

因此，在前 $4\triangle$ 轮次之后的每个配置中，没有非根处理器的寄存器值为 0。以上证明了断言 1。

为了证明断言 2，注意根在每 $\triangle$ 轮次中反复将距离 0 写入其寄存器。设 $c_1$ 为这些 $\triangle$ 轮次后的配置。

每个处理器读取根的寄存器，然后在 $c_1$ 之后的 $4\triangle$ 轮次中将其写入自己的寄存器。在此写操作中，处理器将 1 赋给其自己的寄存器。任何进一步读取根寄存器的操作都会返回值 0；因此，在前 $\triangle+ 4\triangle$ 轮次之后，每个根邻居的寄存器值为 1。因此，断言 2 也成立。

*归纳步骤：*

（假设 $k \geq 0$ 时正确，并证明 $k+1$ 时正确）

设 $m \geq k$ 为前 $\triangle+ 4k\triangle$ 轮次后的配置 $c_{4k}$ 中的最小浮动距离。在 $c_{4k}$ 之后的 $4\triangle$ 轮次中，每个读取 $m$ 并选择 $m$ 作为最小值的处理器将 $m+1$ 赋给其距离并写入该值。因此，在配置 $c_{4(k+1)}$ 中，最小浮动距离值为 $m+1$。这证明了断言 1。

由于最小浮动距离为 $m \geq k$，显然每个处理器读取距离为 $k$ 的邻居处理器的距离并将其距离赋值为 $k+1$。（完）

---

The next corollary is implied by lemma 2.1. Note that once the value in the registers of every processor is equal to its distance from the root, a processor $P_i$ chooses its parent to be the parent in the first *BFS* tree — $P_i$ chooses the first neighbor according to $\alpha_i$, with distance smaller than its own.

下一个推论由引理 2.1 推导得出。注意，一旦每个处理器的寄存器中的值等于其与根的距离，处理器 $P_i$ 会选择其父节点为第一个 *BFS* 树中的父节点——$P_i$ 根据 $\alpha_i$ 选择第一个距离小于其自身距离的邻居。

---

> COROLLARY 2.1: The algorithm presented above is self-stabilizing for $ST$.
>
> 推论 2.1：上述算法对于 $ST$ 是自稳定的。
