# note_2_5_极大匹配

包含 2.9 章部分内容和 PPT 内容。

## Applications to Computer Networks

- Matching in wireless networks
  - Cognitive Radio Networks
  - Device-to-Device (D2D) Communications
- Cloud and edge computing
  - Resource allocation
  - Load-balancing

## Variant Function

main purpose:

- To prove convergence by using a bounded function over a configuration set.
- Can be used to estimate the number of steps required to reach a safe configuration.

---

In a variant function, what property must the function exhibit when processors execute a step?

The function value must monotonically decrease or increase.

What does the term "safe configuration" refer to in the context of the variant function technique?

A configuration after which no changes occur.

## Example: Self-Stabilizing Maximal Matching

### Maximal Matching 极大匹配辨析

A maximal matching cannot be extended by adding more edges, while a maximum matching contains the largest possible number of edges.

给定一个二分图 $G$，在 $G$ 的一个子图 $M$ 中，$M$ 的边集 $E$ 中的任意两条边都不依附于同一个顶点，则称 $M$ 是一个匹配。极大匹配（Maximal Matching）是指在当前已完成的匹配下，无法再通过增加未完成匹配的边的方式来增加匹配的边数。最大匹配（maximum matching）是所有极大匹配当中边数最大的一个匹配。选择这样的边数最大的子集称为图的最大匹配问题。如果一个匹配中，图中的每个顶点都和图中某条边相关联，则称此匹配为完全匹配，也称作完备匹配。

### 几种状态的思考和记忆

> 极大匹配任务的合法执行集 $MM$ 包括所有处理器的指针值**固定并形成极大匹配**的每次执行。给定一个配置 $c_l$，我们说处理器 $P_i$ 是：
>
> - 在 $c_l$ 中是 $matched$，如果 $P_i$ 有一个邻居 $P_j$，使得 ${pointer}_i = j$ 且 ${pointer}_j = i$。
> - 在 $c_l$ 中是 $single$，如果 ${pointer}_i = null$ 且 $P_i$ 的每个邻居都是 matched 的。
> - 在 $c_l$ 中是 $waiting$，如果 $P_i$ 有一个邻居 $P_j$，使得 ${pointer}_i = j$ 且 ${pointer}_j = null$。
> - 在 $c_l$ 中是 $free$，如果 ${pointer}_i = null$ 且存在一个邻居 $P_j$，使得 $P_j$ 没有 matched。
> - 在 $c_l$ 中是 $chaining$，如果存在一个邻居 $P_j$，使得 ${pointer}_i = j$ 且 ${pointer}_j = k, k \neq i$。

首先对于一个已经成型的极大匹配，图中只有 matched 和 single 两种状态的处理器，这两种状态是一种完结的状态，matched 表示我形成了一对匹配，single 表示我的邻居都匹配了就让我一个人单着。

然后 waiting、free、chaining 都是三个中间状态，而且一个比一个更原始（理解为离到达最终状态的步骤很远）。

- 比如 waiting 就是我这个处理器已经指向了另一个处理器，并且明确确定另一个处理器接下来就要指向我了（目前还是 null，下一个步骤就是 matched 了）。
- chaining 则是你（$i$）指向了 $j$，结果 $j$ 指向了另一个 $k$，那这是必然不能继续的。所幸并未指明 $k$ 指向谁（也不关心），所以你应该另寻其他邻居处理器：
  - 假如有，那么你就应该等着去连接它，自己的状态变成 free（为什么不是 waiting，因为还没修改寄存器的值，改完 null 再通过代码第 6 行变为 waiting），同时把它的状态由 chaining 变为 waiting（其实你能发现它和 $i$ 的境况无比相像，也是 chaining，只不过运气好现在和你匹配上了，变成了 waiting）；
  - 假如没有，那也有可能是有未匹配的邻居，这种情况就是变成 free；如果邻居都是 matched，那么就进入最终状态 single 了。

还可以比较 single 和 free。虽然 $pointer$ 都是 $null$，但是 free（或者说能/将要变到这个状态）就说明还有没有 matched 的邻居，而 single 是真的什么邻居都 matched 了。

最后看一下变元函数 $VF$ 的返回值——向量 $(m+s, w, f, c)$。$m$ 和 $s$ 放在一起是因为他们都是最终状态；$w, f, c$ 这三个按照顺序越来越原始。故而 $VF$ 的值按字典顺序进行比较就是顺理成章的，并且算法的每次指针值变化都会增加 $VF$ 的值，也就是向最终状态 $VF(c) = (n, 0, 0, 0)$ 靠近。

> the system reaches a safe configuration within $O(n^3)$ pointer-value changes.
>
> 系统在 $O(n^3)$ 次指针值变化内达到安全配置。
