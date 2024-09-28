# note_2_4_公平合成

包含 2.7 章。这一章概念的内容比较好懂，但是给出的一般通信图的互斥的例子实在是很难懂，万幸老师也没很深入的讲。

## Fair Composition 公平合成

### 概念相关

**$\mathcal{AL}_{i+1}$ cannot detect whether the algorithms $\mathcal{AL}_1$ to $\mathcal{AL}_i$ have stabilized, but $\mathcal{AL}_{i+1}$ is executed as if these algorithms $\mathcal{AL}_1$ to $\mathcal{AL}_i$ have done so**.

**The server algorithm ensures that some properties will hold, and these properties are later used by the client algorithm.**

**$\mathcal{AL}_{i+1}$ 无法检测 $\mathcal{AL}_1$ 到 $\mathcal{AL}_i$ 是否已经稳定，但 $\mathcal{AL}_{i+1}$ 的执行方式就像这些算法 $\mathcal{AL}_1$ 到 $\mathcal{AL}_i$ 已经稳定一样**。

**服务器算法确保某些属性将保持，这些属性随后被客户端算法使用。**

### A-projection（A-投影）

#### 补课：Cartesian product（笛卡尔积）和 projection（投影）

##### 笛卡尔积

笛卡尔积 $A_i \times B_i$ 表示所有可能的 $(a_i, b_i)$ 组合，其中 $a_i \in A_i$，$b_i \in B_i$。

假设有两个关系表（$R$）和（$S$）：

**R:**

| A    | B    |
| ---- | ---- |
| 1    | 2    |
| 3    | 4    |

**S:**

| C    | D    |
| ---- | ---- |
| 5    | 6    |
| 7    | 8    |

笛卡尔积（$R \times S$）的结果是：

| A | B | C | D |
|---|---|---|---|
| 1 | 2 | 5 | 6 |
| 1 | 2 | 7 | 8 |
| 3 | 4 | 5 | 6 |
| 3 | 4 | 7 | 8 |

##### 投影

假设有一个关系表 ($T$)：

**T:**

| A    | B    | C    |
| ---- | ---- | ---- |
| 1    | 2    | 3    |
| 4    | 5    | 6    |
| 7    | 8    | 9    |

对（$T$）进行投影，选择列（$A$）和（$C$）：

| A | C |
|---|---|
| 1 | 3 |
| 4 | 6 |
| 7 | 9 |

#### A-投影的来由（作用）

A-投影用于描述在组合算法中的状态转换。具体来说：

- 当处理器 $P_i$ 执行 $\mathcal{AL}_1$ 的步骤时，它只使用 $A_i$ 部分的状态，忽略 $B_i$ 部分。
- 当处理器 $P_i$ 执行 $\mathcal{AL}_2$ 的步骤时，它使用 $A_i$ 和 $B_i$ 部分的状态来计算新的 $B_i$ 部分。

只用 $A_i$ 部分，所以需要投影。

---

> We say that *algorithm $\mathcal{AL}_2$ is self-stabilizing for task $\mathcal{T}_2$ given task $\mathcal{T}_1$* **if any fair execution of $\mathcal{AL}_2$ that has an *A-projection* in $\mathcal{T}_1$ has a suffix in $\mathcal{T}_2$**. Finally, an algorithm $\mathcal{AL}$ is a *fair composition* of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ if, in $\mathcal{AL}$, every processor execute steps of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ alternately. In fact it is sufficient that every processor executes steps of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ infinitely often. Note that, for an execution $E$ of $\mathcal{AL}$, the *A-projection* of $E$ is a sub-execution of E corresponding to a fair execution of the server algorithm $\mathcal{AL}_1$.
>
> 我们说 *算法 $\mathcal{AL}_2$ 在任务 $\mathcal{T}_1$ 给定的情况下是自稳定的，用于任务 $\mathcal{T}_2$*，**如果 $\mathcal{AL}_2$ 的任何公平执行在 $\mathcal{T}_1$ 中有一个 *A-投影*，则它在 $\mathcal{T}_2$ 中有一个后缀**。最后，如果在算法 $\mathcal{AL}$ 中，每个处理器交替执行 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的步骤，那么算法 $\mathcal{AL}$ 是 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的 *公平组合*。实际上，只要每个处理器无限次地执行 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的步骤就足够了。注意，对于 $\mathcal{AL}$ 的执行 $E$，$E$ 的 *A-投影* 是 $E$ 的一个子执行，对应于服务器算法 $\mathcal{AL}_1$ 的公平执行。

假设 $\mathcal{AL}_2$ 的一个公平执行序列 $E$，其 A-投影在任务 $\mathcal{T}_1$ 中。这意味着在执行序列 $E$ 中，所有处理器在 $\mathcal{AL}_1$ 中的状态都已经达到了 $\mathcal{T}_1$ 的要求。

$\mathcal{AL}_2$ 的设计假设 $\mathcal{AL}_1$ 已经稳定（即满足 $\mathcal{T}_1$ 的要求）。当 $\mathcal{AL}_1$ 稳定后，$\mathcal{AL}_2$ 可以在此基础上运行，并逐步达到其自身的稳定状态（即满足 $\mathcal{T}_2$ 的要求）。

### 构建复杂任务的自稳定算法

> THEOREM 2.2: Assume that $\mathcal{AL}_2$ is self-stabilizing for a task $\mathcal{T}_2$ given task $\mathcal{T}_1$ If $\mathcal{AL}_1$ is self-stabilizing for $\mathcal{T}_1$, then the fair composition of $\mathcal{AL}_1$ and $\mathcal{AL}_2$ is self-stabilizing for $\mathcal{T}_2$.
>
> 定理 2.2：假设 $\mathcal{AL}_2$ 在任务 $\mathcal{T}_1$ 给定的情况下是自稳定的，用于任务 $\mathcal{T}_2$。如果 $\mathcal{AL}_1$ 是 $\mathcal{T}_1$ 的自稳定算法，那么 $\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合是 $\mathcal{T}_2$ 的自稳定算法。

定理 2.2 提供了一种构建复杂任务的自稳定算法的一般方法。

- 给定一个任务 $\mathcal{T}_2$，我们希望为其构建这样的算法，首先定义一个任务 $\mathcal{T}_1$ 并构建一个在 $\mathcal{T}_1$ 给定的情况下对 $\mathcal{T}_2$ 自稳定的算法 $\mathcal{AL}_2$，
- 然后构建一个对 $\mathcal{T}_1$ 自稳定的算法 $\mathcal{AL}_1$。$\mathcal{AL}_1$ 和 $\mathcal{AL}_2$ 的公平组合就是所需的算法。注意，这种方法不要求算法 $\mathcal{AL}_1$ 达到“稳定状态”，即通信寄存器（或处理器 $P_i$ 状态中的任何其他组件）从未改变。

## Mutual Exclusion for General Communication Graphs 一般通信图的互斥

### Euler tour 欧拉回路（欧拉巡游）

欧拉回路（Euler Tour）是图论中的一个重要概念，指的是在一个图中找到一条路径，使得每条边恰好经过一次，并且路径的起点和终点相同。如果这样的路径存在，则称该图具有欧拉回路。

给了个链接，看的还是有点吃力：<https://en.wikipedia.org/wiki/Euler_tour_technique>
