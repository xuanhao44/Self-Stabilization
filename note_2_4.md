# note_2_4_公平合成

包含 2.7 章。

这一章概念的内容比较好懂，但是给出的一般通信图的互斥的例子实在是很难懂，万幸老师也没很深入的讲。以下概念部分内容均复制自原文。

In this section, we introduce the technique of fair composition of self-stabilizing algorithms. The idea is to compose self-stabilizing algorithms $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_k$ so that the stabilized behavior (roughly speaking, output) of $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_i$ is used (as an input) by $\mathcal{AL}_{i+1}$. The algorithms are executed in a fair fashion—each processor executes a step of each algorithm infinitely often. **$\mathcal{AL}_{i+1}$ cannot detect whether the algorithms $\mathcal{AL}_1$ to $\mathcal{AL}_i$ have stabilized, but $\mathcal{AL}_{i+1}$ is executed as if these algorithms $\mathcal{AL}_1$ to $\mathcal{AL}_i$ have done so**. Thus, when every algorithm up to the $i$ th algorithm has stabilized, $\mathcal{AL}_{i+1}$ is in an arbitrary state from which it starts converging to a legal behavior.

在本节中，我们介绍了自稳定算法的公平组合技术。其思想是将自稳定算法 $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_k$ 组合起来，使得 $\mathcal{AL}_1,\mathcal{AL}_2, · · · ,\mathcal{AL}_i$ 的稳定行为（粗略地说，输出）被 $\mathcal{AL}_{i+1}$ 用作输入。这些算法以公平的方式执行——每个处理器无限次地执行每个算法的一步。**$\mathcal{AL}_{i+1}$ 无法检测 $\mathcal{AL}_1$ 到 $\mathcal{AL}_i$ 是否已经稳定，但 $\mathcal{AL}_{i+1}$ 的执行方式就像这些算法 $\mathcal{AL}_1$ 到 $\mathcal{AL}_i$ 已经稳定一样**。因此，当每个算法直到第 $i$ 个算法都已稳定时，$\mathcal{AL}_{i+1}$ 处于一个任意状态，从该状态开始收敛到合法行为。

> THEOREM 2.2: Assume that $\mathcal{AL}_2$ is self-stabilizing for a task $\mathcal{T}_2$ given task $\mathcal{T}_1$ If $\mathcal{AL}_1$ is self-stabilizing for $\mathcal{T}_1$, then the fair composition of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ is self-stabilizing for $\mathcal{T}_2$.
>
> 定理 2.2：假设 $\mathcal{AL}_2$ 在任务 $\mathcal{T}_1$ 给定的情况下是自稳定的，用于任务 $\mathcal{T}_2$。如果 $\mathcal{AL}_1$ 是 $\mathcal{T}_1$ 的自稳定算法，那么 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合是 $\mathcal{T}_2$ 的自稳定算法。

定理 2.2 提供了一种构建复杂任务的自稳定算法的一般方法。

- 给定一个任务 $\mathcal{T}_2$，我们希望为其构建这样的算法，首先定义一个任务 $\mathcal{T}_1$ 并构建一个在 $\mathcal{T}_1$ 给定的情况下对 $\mathcal{T}_2$ 自稳定的算法 $\mathcal{AL}_2$，
- 然后构建一个对 $\mathcal{T}_1$ 自稳定的算法 $\mathcal{AL}_1$。$\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合就是所需的算法。注意，这种方法不要求算法 $\mathcal{AL}_1$ 达到“稳定状态”，即通信寄存器（或处理器 $P_i$ 状态中的任何其他组件）从未改变。
