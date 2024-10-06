# 4.3 Self-Stabilizing Ranking: Converting an Id-based System to a Special-processor System

In this section we consider the following conversion: A given self-stabilizing algorithm for a system in which each processor has a unique identifier is converted to work in a system in which all the processors are identical except a single special processor. To do this, we design a self-stabilizing algorithm that assigns unique identifiers to the processors. The *ranking* task is to assign each of the $n$ processors in the system with a unique identifier in the range 1 to $n$.

在本节中，我们考虑以下转换：将一个给定的自稳定算法从每个处理器都有唯一标识符的系统转换为所有处理器都相同，除了一个特殊处理器的系统。为此，我们设计了一个自稳定算法，为处理器分配唯一标识符。*排名* 任务是为系统中的每个 $n$ 个处理器分配一个范围为 1 到 $n$ 的唯一标识符。

The self-stabilizing spanning-tree construction of section 2.5 is composed with a self-stabilizing counting algorithm and a self-stabilizing naming algorithm to form the self-stabilizing ranking algorithm.

第 2.5 节的自稳定生成树构造与自稳定计数算法和自稳定命名算法结合，形成自稳定排名算法。

The self-stabilizing counting algorithm assumes a **rooted spanning tree** system in which every processor knows its parent and children in the tree. Each processor $P_i$ has a variable ${count}_i$ whose value is written by $P_i$ in the communication register that is shared with the parent of $P_i$ in the tree. The self-stabilizing counting algorithm reaches a safe configuration in which **the value of ${count}_i$ is the number of processors in the subtree rooted at $P_i$ including $P_i$ itself**. The algorithm appears in figure 4.3. A processor $P_i$ with no children repeatedly assigns 1 to ${count}_i$. Let the *height* of a processor $P_i$ in a rooted tree be the length of the longest path from $P_i$ to a leaf such that the path does not include the root. Once the value of the count variables of all the leaves is 1, we consider processors $P_j$ of height 1. Clearly, when $P_j$ finishes executing a complete iteration of the do forever loop, $P_j$ assigns ${count}_j$ by the correct number of processors in its subtree. **The correctness proof continues by induction on the height of a processor**.

自稳定计数算法假设一个**有根生成树**系统，其中每个处理器都知道其在树中的父节点和子节点。每个处理器 $P_i$ 有一个变量 ${count}_i$，其值由 $P_i$ 写入与其父节点共享的通信寄存器中。自稳定计数算法达到一个安全配置，其中 ${count}_i$ 的值是**以 $P_i$ 为根的子树中的处理器数量，包括 $P_i$ 本身**。该算法见图 4.3。没有子节点的处理器 $P_i$ 反复将 1 分配给 ${count}_i$。设处理器 $P_i$ 在有根树中的 *高度* 为从 $P_i$ 到叶节点的最长路径的长度，该路径不包括根节点。一旦所有叶节点的计数变量的值为 1，我们考虑高度为 1 的处理器 $P_j$。显然，当 $P_j$ 完成 do forever 循环的完整迭代时，$P_j$ 将根据其子树中的处理器数量正确分配 ${count}_j$。**正确性证明通过处理器高度的归纳法继续**。

The self-stabilizing counting algorithm is an example of the *self-stabilizing convergecast technique*. Intuitively, the *self-stabilizing convergecast* technique is based on the fact that **the correct information is repeatedly communicated from the leaves of the tree toward the root**.

自稳定计数算法是 *自稳定汇聚广播技术* 的一个例子。直观地说，*自稳定汇聚广播* 技术基于以下事实：**正确的信息反复从树的叶节点传递到根节点**。

The naming algorithm uses the value of the *count* fields to assign unique identifiers to the processors. The code for the naming algorithm appears in figure 4.4. The identifier of a processor is stored in the ${ID}_i$ variable. A processor that executes the do loop of lines 4 through 8 or lines 14 through 18 executes one iteration of the loop for each processor in $children(i)$. The order of the iteration is arbitrary but fixed, say according to the internal labels of the links attached to $P_i$.

命名算法使用 *计数* 字段的值为处理器分配唯一标识符。命名算法的代码见图 4.4。处理器的标识符存储在 ${ID}_i$ 变量中。执行第 4 到 8 行或第 14 到 18 行的 do 循环的处理器对 $children(i)$ 中的每个处理器执行一次循环迭代。迭代的顺序是任意但固定的，比如根据连接到 $P_i$ 的链接的内部标签。

![figure_4.3](images/figure_4.3.png)

![figure_4.4](images/figure_4.4.png)

The proof of the stabilization of the naming algorithm (given a safe configuration of the counting algorithm) is by induction on the distance of the processors from the root. The induction starts with the single processor that is at distance 0 from the root and continues with processors that are at a larger distance. The details are left as an exercise to the reader.

命名算法的稳定性证明（假设计数算法的配置是安全的）是基于处理器到根的距离的归纳法。归纳从距离根为 0 的单个处理器开始，并继续处理距离较远的处理器。具体细节留作读者练习。

![figure_4.5](images/figure_4.5.png)

Before continuing to the next conversion method, we present an elegant self-stabilizing counting algorithm for a *non-rooted tree* $T$.

Every processor in such a system knows which of its attached links belong to the tree $T$.

Each processor $P_i$ has a variable ${count}_i[j]$ for every neighbor $P_j$. In a safe configuration, the value of every ${count}_i[j]$ is the number of processors in a subtree of $T$ that is obtained by removing the edge between $P_i$ and $P_j$. Each such removal of an edge splits the tree into two subtrees; $P_i$ belongs to one such subtree and $P_j$ to the other. The value ${count}_i[j]$ is related to the number of processors in the subtree to which $P_i$ belongs.

The code of the algorithm appears in figure 4.5.

Let the *height* of a communication register $r_{ij}$ be the depth of the tree rooted at $P_i$ upon removing the edge between $P_i$ and $P_j$, where the depth of a rooted tree is the maximal number of edges from the root to a leaf.

The correctness proof is by induction on the height of the registers. In the first cycle of every fair execution, the $r_{ij}.count$ field of every register of height 0 (a register of a leaf) is assigned the value 1 by $P_i$. Clearly, every further assignment of the leaf processor $P_i$ in $r_{ij}.count$ does not change the value of $r_{ij}.count$.

Now observe that the value of every register of height 2 is computed using only values of registers of height 1. In general, the value of every register of height $h$ is computed using the values of registers of smaller height. The height of the registers is bounded by the diameter of the system $d$; hence, it holds that a safe configuration is reached within $O(d)$ cycles.

The counting algorithm can be used to elect a leader (or two leaders if symmetry cannot be broken) in the tree and assign unique identifiers to the processors (assuming that the symmetry in the two leaders case can be broken).

在继续下一个转换方法之前，我们提出一个优雅的*非根树* $T$ 的自稳定计数算法。这样的系统中的每个处理器都知道其连接的链接属于树 $T$。

每个处理器 $P_i$ 对每个邻居 $P_j$ 都有一个变量 ${count}_i[j]$。在安全配置中，每个 ${count}_i[j]$ 的值是通过移除 $P_i$ 和 $P_j$ 之间的边获得的 $T$ 的子树中的处理器数量。每次移除一条边都会将树分成两棵子树；$P_i$ 属于其中一棵子树，$P_j$ 属于另一棵子树。${count}_i[j]$ 的值与 $P_i$ 所属子树中的处理器数量有关。

算法的代码见图 4.5。

设通信寄存器 $r_{ij}$ 的 *高度* 为移除 $P_i$ 和 $P_j$ 之间的边后以 $P_i$ 为根的树的深度，其中有根树的深度是从根到叶的最大边数。正确性证明是基于寄存器高度的归纳法。

在每次公平执行的第一个周期中，高度为 0 的每个寄存器（叶节点的寄存器）的 $r_{ij}.count$ 字段被 $P_i$ 赋值为 1。显然，叶节点处理器 $P_i$ 在 $r_{ij}.count$ 中的进一步赋值不会改变 $r_{ij}.count$ 的值。

现在观察，高度为 2 的每个寄存器的值仅使用高度为 1 的寄存器的值计算。一般来说，高度为 $h$ 的每个寄存器的值是使用较小高度的寄存器的值计算的。寄存器的高度受系统直径 $d$ 的限制；因此，在 $O(d)$ 周期内达到安全配置。

计数算法可用于在树中选举一个领导者（如果对称性不能打破，则选举两个领导者）并为处理器分配唯一标识符（假设在两个领导者的情况下可以打破对称性）。
