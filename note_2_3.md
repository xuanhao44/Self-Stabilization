# 2_3_生成树构造

包含 2.5 章，以及 PPT 上内容。

<!-- TOC -->

- [2\_3\_生成树构造](#2_3_生成树构造)
  - [Switching Loop 交换机环路](#switching-loop-交换机环路)
  - [Spanning Tree Protocol (STP)](#spanning-tree-protocol-stp)
    - [STP History](#stp-history)
  - [BFS Construction](#bfs-construction)
    - [sequential, non-distributed BFT](#sequential-non-distributed-bft)
    - [Non-self-stabilizing Distributed BFT](#non-self-stabilizing-distributed-bft)
    - [Self-stabilizing Distributed BFT](#self-stabilizing-distributed-bft)
    - [此外谈谈我的看法](#此外谈谈我的看法)
  - [自稳定算法证明](#自稳定算法证明)

<!-- /TOC -->

## Switching Loop 交换机环路

A switching loop happens when multiple data-link layer paths exist between two nodes in a network.

1. **Broadcast Storms**
   1. Switching loops can trigger broadcast storms, where an Ethernet frame gets forwarded by switches out of every port.
   2. This results in the frame being repeatedly broadcasted, causing network congestion.

2. **No TTL Mechanism**. Unlike some network layers, the data-link layer doesn't use a Time-to-Live (TTL) mechanism to limit frame propagation.

3. **Endless Proliferation**
   1. When a frame enters a looped network topology, it can keep circulating indefinitely.
   2. This uncontrolled propagation can disrupt network operations and lead to performance issues.

Switching loops can have severe consequences in network communication, including broadcast storms and persistent frame proliferation, due to the absence of a TTL mechanism at the data-link layer. It's essential to prevent and address switching loops to maintain network stability.

## Spanning Tree Protocol (STP)

**Loop-Free Logical Topology.**

STP (Spanning Tree Protocol) is designed to establish a loop-free logical topology within Ethernet switches.

1. **Creating a Spanning Tree**
   1. STP accomplishes this by creating a spanning tree, a hierarchical structure that designates specific links as active while disabling others.
   2. This arrangement ensures that there's always a single active path between any two network nodes.

2. **Preventing Broadcast Storms**
   1. A primary goal of STP is to prevent broadcast storms that can occur in networks with loops.
   2. By carefully controlling the active paths, STP avoids the conditions that lead to these storms.

3. **Backup Links**
   1. In addition to maintaining active paths, STP can incorporate backup links.
   2. These backup links become active if a primary link fails, ensuring network resilience and continuity.

Spanning Tree Protocol (STP) is a critical tool for creating a stable and loop-free network topology. It prevents broadcast storms by establishing a spanning tree structure and can include backup links to maintain network reliability in the event of link failures.

注：只要生成了生成树，那么就能防止路径过多；很遗憾的是，STP 协议的生成树构造算法是分布式的，但是却不是自稳定的。不过自稳定生成树构造算法在许多需要高可靠性和容错能力的分布式系统中都有应用。

### STP History

1. **Origins of STP**
   1. STP (Spanning Tree Protocol) was initially standardized as IEEE 802.1D.
   2. Interestingly, the first STP algorithm was developed by Radia Perlman as part of her master's thesis.

2. **Rapid Spanning Tree Protocol (RSTP)**
   1. RSTP, introduced by IEEE in 2001 as IEEE 802.1w, improved upon the original STP.
   2. It aimed to reduce convergence time and enhance network responsiveness.

3. **Integration into IEEE 802.1Q-2014**
   1. Today, the functionality of IEEE 802.1D (STP), IEEE 802.1w (RSTP), and multiple spanning-tree (802.1s) protocols is incorporated into the IEEE 802.1Q-2014 standard.
   2. This comprehensive standard is used for Local and Metropolitan Area Networks (LANs/MANs), providing a unified approach to spanning tree operations.

STP has evolved from its initial standardization as IEEE 802.1D to the development of RSTP (802.1w) and its eventual integration into the IEEE 802.1Q-2014 standard. These advancements have significantly improved the efficiency and reliability of network spanning tree operations.

## BFS Construction

1. **Breadth-First Search (BFS)**
   1. Breadth-First Search is a fundamental algorithm used for traversing networks, graphs, or trees.
   2. It systematically explores nodes in a structured manner, starting from a designated node called the root.

2. **The Root Node**
   1. In BFS, the starting node from which the exploration begins is known as the root node.
   2. The algorithm's progress radiates outwards from this root.

3. **Exploring Neighboring Nodes**
   1. BFS focuses on exploring neighboring nodes before moving to nodes at deeper levels.
   2. It ensures that all nodes at a particular level are visited before descending further.

4. **Constructing a Directed Tree**
   1. The primary goal of BFS is to construct a directed tree, which is a subgraph of the network.
   2. In this tree, all edges point towards the root node, creating a hierarchical structure.

5. **Shortest Paths to the Root**
   1. One of BFS's key properties is that it guarantees the construction of a directed path from every node to the root.
   2. Importantly, these directed paths are also the shortest paths within the network.

Breadth-First Search (BFS) is a powerful algorithm used to explore networks systematically. It begins at a root node, explores neighboring nodes first, and constructs a directed tree where all paths to the root are the shortest possible. This makes BFS invaluable in various applications, including network routing and graph traversal.

### sequential, non-distributed BFT

Here is pseudocode for a **sequential solution, none distributed**, for the problem of the Breadth-First Search (BFS). The output of this construction is a directed tree.

```pseudocode
function BFS(Graph, root):
    // Create an empty queue for nodes to visit
    queue = Queue()
    // Create an empty set to keep track of visited nodes
    visited = Set()
    // Create an empty directed tree (adjacency list)
    tree = {}
    // Initialize the queue with the root node
    queue.enqueue(root)

    // Mark the root node as visited
    visited.add(root)

    // While there are nodes to visit in the queue
    while not queue.isEmpty():
      // Dequeue the next node to visit
      current = queue.dequeue()

      // Add current node to the directed tree
      tree[current] = []

      // Explore neighbors of the current node
      for neighbor in Graph.neighbors(current):
        if neighbor not in visited:
            // Mark neighbor as visited
            visited.add(neighbor)

            // Add neighbor to the directed tree as a child of the current node
            tree[current].append(neighbor)

            // Enqueue neighbor for further exploration
            queue.enqueue(neighbor)

    // Return the directed tree
    return tree
```

This pseudocode outlines the BFS algorithm for constructing a directed tree starting from a given root node in a graph. The resulting directed tree represents the hierarchical structure of the exploration process, with each node pointing to its children in the tree.

### Non-self-stabilizing Distributed BFT

```pseudocode
variables: distance, initially 0 for root and \infty for non-root processors;

root:
  send distance to all neighbors

non-root:
  upon receiving <d> from pj:
    if d+1 < distance then
      distance := d+1;
      parent := j;
      send <distance> to all neighbors;
```

### Self-stabilizing Distributed BFT

![figure_2.1](images/figure_2.1.png)

关于算法更细致的描述在书中，但是 PPT 上有几个要点的总结。

- We will use the shared memory model for this example
- The system consists $n$ processors
- A processor $p_i$ communicates with its neighbor $p_j$ by writing in the communication register $r_{ij}$ and reading from $r_{ji}$
- The output is the constructed tree, which the registers encode
- Each node has a register per neighbor with the fields <child-indication, distance>
- First-BFS: The first-BFT construction selects the ”first” of these paths
  - **Decide on the order using local identifiers**
- The root inform all its neighbors that its distance is 0
- The non-root chooses the minimal distance of its neighbors, adds one and updates its neighbors
- Correct encoding of the distances allows the correct assignment of the parent-child values
  - 正确地编码距离信息可以确保父节点和子节点之间的关系被正确地分配。也就是说，通过准确地记录和传递每个节点到根节点的距离，系统能够正确地确定每个节点的父节点和子节点，从而构建出正确的树结构。

### 此外谈谈我的看法

首先在了解了 BFS 算法，然后复习了非分布式的 BFT 构造算法后，我们又看了非自稳定语境下的分布式 BFT 构造算法，最后我们才来到自稳定的分布式 BFT 算法。

可以比较一下 Non-self-stabilizing Distributed BFT 和 Self-stabilizing Distributed BFT，发现主要区别就是所谓的 First-BFS。它通过特定的规则和机制来保证最后构造的只有一个唯一的生成树。

我们来看算法中和 First-BFS 有关的部分：

${lr}_{mi}.dis = dist - 1$ 很好理解，就是这个节点 $i$ 的邻居 $m$ 是它的最短路径（到根节点）的来源，符合这个条件的节点 $i$ 可能有多个，但是我只要“第一个”，我只将我找到的第一个作为父节点，所以我将 $r_{im}$ 标记为 $<1, dist>$，并且将 $FirstFound$ 标记为 true。

那么之后，首先是对于其他的满足 ${lr}_{mi}.dis = dist - 1$ 的节点 $i$，我不会将其认作父节点，故而 $parent$ 字段就标为 0；其他的就更不可能，所以也是 0。

至于 $distance$ 字段，没什么好需要说的，节点 $i$ 就是单纯在通告它和根节点的距离。

所以你现在知道这个 $parent$ 的作用了，就是唯一的父节点而生的。这样最后一张无向图中就诞生了一个唯一的有根树。

最后是怎么理解 **Decide on the order using local identifiers**：我们来看原文。

> Let $\alpha = (\alpha_1, \alpha_2, ..., \alpha_n)$ be the arbitrary ordering of the edges incident to each node $v_i \in V$. The first *BFS* tree of a communication graph $G$ is uniquely defined by the choice of the root $v_1$ and $\alpha$. When a node $v_i$ of distance $x +1$ from $v_1$ has more than a single neighbor of distance $x$ from $v_1$, **$v_i$ is connected to its first neighbor according to $\alpha_i$**, whose distance from $v_1$ is $x$.
>
> 设 $\alpha = (\alpha_1, \alpha_2, ..., \alpha_n)$ 为每个节点 $v_i \in V$ 的边的任意排序。通信图 $G$ 的 *第一个 BFS 树* 由根 $v_1$ 和 $\alpha$ 唯一定义。当距离 $v_1$ 为 $x+1$ 的节点 $v_i$ 有多个距离 $v_1$ 为 $x$ 的邻居时，**$v_i$ 根据 $\alpha_i$ 连接到其第一个邻居**，该邻居距离 $v_1$ 为 $x$。

$\alpha$ 是一个任意的序列，那么显然在选择最近的邻居的时候，这个序列是谁的呢？自然就是当前正在做选择的这个处理器对其他处理器的标号，也就是本地编号（local identifiers）。

## 自稳定算法证明

**The task $ST$ of legitimate sequences is defined as the set of all configuration sequences in which every configuration encodes a *BFS* tree of the communication graph. In fact, a particular *BFS* tree called the *first BFS tree* is encoded.** Let $\alpha = (\alpha_1, \alpha_2, ..., \alpha_n)$ be the arbitrary ordering of the edges incident to each node $v_i \in V$. The first *BFS* tree of a communication graph $G$ is uniquely defined by the choice of the root $v_1$ and $\alpha$. When a node $v_i$ of distance $x +1$ from $v_1$ has more than a single neighbor of distance $x$ from $v_1$, **$v_i$ is connected to its first neighbor according to $\alpha_i$**, whose distance from $v_1$ is $x$. In the lemma below, we use the definition of the first *BFS* tree to characterize the set of safe configurations for the algorithm.

The lemma below shows that, in every execution, a safe configuration is reached. **We use $\triangle$ to denote the maximum number of links adjacent to a processor**, and use the following definitions of *floating distances* and *smallest floating distance* in our proof.

> DEFINITION 2.1: A floating distance in some configuration c is a value in a register $r_{ij}.dis$ that is smaller than the distance of $P_i$ from the root. The smallest floating distance in some configuration $c$ is the smallest value among the floating distances.
>
> LEMMA 2.1: For every $k > 0$ and for every configuration that follows $\triangle + 4k\triangle$ rounds, it holds that:
>
> *Assertion 1:* If there exists a floating distance, then the value of the smallest floating distance is at least $k$.
>
> *Assertion 2:* The value in the registers of every processor that is within distance $k$ from the root is equal to its distance from the root.

The next corollary is implied by lemma 2.1. Note that once the value in the registers of every processor is equal to its distance from the root, a processor $P_i$ chooses its parent to be the parent in the first *BFS* tree — $P_i$ chooses the first neighbor according to $\alpha_i$, with distance smaller than its own.

> COROLLARY 2.1: The algorithm presented above is self-stabilizing for $ST$.

**合法序列任务 $ST$ 定义为所有配置序列的集合，其中每个配置都编码了通信图的一个 *BFS* 树。实际上，编码的是一个特定的 *BFS* 树，称为 *第一个 BFS 树*。**设 $\alpha = (\alpha_1, \alpha_2, ..., \alpha_n)$ 为每个节点 $v_i \in V$ 的边的任意排序。通信图 $G$ 的 *第一个 BFS 树* 由根 $v_1$ 和 $\alpha$ 唯一定义。当距离 $v_1$ 为 $x+1$ 的节点 $v_i$ 有多个距离 $v_1$ 为 $x$ 的邻居时，**$v_i$ 根据 $\alpha_i$ 连接到其第一个邻居**，该邻居距离 $v_1$ 为 $x$。在下面的引理中，我们使用第一个 *BFS* 树的定义来描述算法的一组安全配置。

下面的引理表明，在每次执行中，都会达到一个安全配置。**我们用 $\triangle$ 表示与处理器相邻的最大链接数**，并在证明中使用以下 *浮动距离* 和 *最小浮动距离* 的定义。

> 定义 2.1：在某个配置 $c$ 中，浮动距离是寄存器 $r_{ij}.dis$ 中的一个值，该值小于 $P_i$ 到根的距离。在某个配置 $c$ 中，最小浮动距离是浮动距离中的最小值。
>
> 引理 2.1：对于每个 $k>0$ 和每个经过 $\triangle + 4k\triangle$ 轮次后的配置，满足以下条件：
>
> *断言 1：*如果存在浮动距离，那么最小浮动距离的值至少为 $k$。
>
> *断言 2：*每个距离根为 $k$ 以内的处理器的寄存器中的值等于其与根的距离。

下一个推论由引理 2.1 推导得出。注意，一旦每个处理器的寄存器中的值等于其与根的距离，处理器 $P_i$ 会选择其父节点为第一个 *BFS* 树中的父节点——$P_i$ 根据 $\alpha_i$ 选择第一个距离小于其自身距离的邻居。

> 推论 2.1：上述算法对于 $ST$ 是自稳定的。

---

关于引理 2.1 的证明，使用了归纳推理法，也就是首先证明 $k=1$ 的时候成立，然后再证明若 $k$ 的情况成立能推导出 $k+1$ 的情况也成立。

关于 $\triangle$：与处理器相邻的最大链接数，其实就是所有 $\delta$ 中的最大值 $\max_{\delta}$（参数 $\delta$ 是处理器的邻居数量）。它就是来保证在 $\triangle$ 轮后，$\delta$ 循环一定结束（仅包含一个读/写操作的循环）。

对于 root 来说，$\triangle$ 轮完成一个循环；对于其他处理器来说，$4 \triangle$ 轮完成一个循环。

此外，在 PPT 上还有这个证明的**简化版本**。并不适用 $\triangle$，而是引入了之前学过的异步轮的概念，将 $\triangle+ 4k\triangle$ 轮次这个实际上不是很完备的概念换成了 $2k$ 个异步轮。

---

最后有什么感悟：明白了自稳定算法的**永不停止，反复通信**的含义。

在 2.6 中，互斥算法通过 $p_1$ 的每 $n$ 轮的值的修改保证了每个处理器都会在公平执行会修改 $x$ 的值，也就是任务 $ME$。在 2.5 本节中，最后达到的安全配置却并不是会一直变，而是得到了一个不变的生成树。可能会奇怪这难道是永不停止吗？其实是的。请看概念：

> **自稳定算法永不终止，处理器必须反复与其邻居通信。**在共享内存模型中，处理器必须反复读取其邻居的寄存器；在消息传递模型中，处理器必须不断发送和接收消息。

我们并没有要求它一定要写，也没有要求它一定要一直变，但是确实需要反复读取其邻居的寄存器（状态检查）。所以也是可以规定这样的任务的，不要太局限视野了。
