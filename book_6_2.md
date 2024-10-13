# 6.2 Stabilization in Spite of Napping

We are interested in clock synchronization algorithms that can tolerate hybrid faults: these should work starting from an arbitrary initial configuration and should tolerate processors that exhibit faults during and after convergence.

Several self-stabilizing algorithms that synchronize digital clocks in the presence of (permanent) faults are known. An algorithm that tolerates both transient and permanent faults is, of course, more resilient to faults than an algorithm that tolerates only one of these types of faults.

我们感兴趣的是能够容忍混合故障的时钟同步算法：这些算法应该从任意初始配置开始工作，并且应该能够容忍在收敛期间和之后出现故障的处理器。

已有几种同步数字时钟的自稳定算法可以在（永久）故障存在下工作。一种能够容忍瞬时和永久故障的算法，当然比只容忍其中一种故障的算法更具弹性。

The first set of results concerns a complete communication graph in which each processor can communicate with all other processors. **A self-stabilizing clock synchronization algorithm copes with permanent *napping* faults if processors can repeatedly stop and (possibly) resume operation during and after the convergence of the algorithm to a safe configuration**. Note that napping faults are more severe than crash failures, since a crashed processor can be seen as a napping processor that does not resume operation.

**A clock synchronization algorithm that copes with transient and napping faults is called a *wait-free self-stabilizing* clock-synchronization algorithm**. A non-faulty processor $P_i$ synchronizes its clock even when all $n-1$ other processors are faulty; in other words, $P_i$ does not wait for other processors to participate in the algorithm.

What is the meaning of synchronization when $n-1$ processors are faulty? How can the single operational processor be synchronized with itself? The answer to these questions is simple: the synchronization algorithm should ensure that **each non-faulty operating processor ignores the faulty processors and increments its clock value by one in every pulse**.

第一个结果集涉及一个完整的通信图，其中每个处理器都可以与所有其他处理器通信。**如果处理器可以在算法收敛到安全配置期间和之后反复停止和（可能）恢复操作，那么一个自稳定的时钟同步算法可以应对永久的 *打盹* 故障**。请注意，打盹故障比崩溃故障更严重，因为崩溃的处理器可以看作是一个不再恢复操作的打盹处理器。

**应对瞬时和打盹故障的时钟同步算法称为 *无等待自稳定* 时钟同步算法**。当所有其他 $n-1$ 个处理器都故障时，一个无故障处理器 $P_i$ 依然同步它的时钟；换句话说，$P_i$ 不等待其他处理器参与算法。

当 $n-1$ 个处理器故障时，同步的意义是什么？单个操作处理器如何与自己同步？这些问题的答案很简单：同步算法应确保**每个无故障操作的处理器忽略故障处理器，并在每个脉冲中将其时钟值增加一**。

A processor $P_i$ *adjusts* its clock in a certain step if the value of its clock is not incremented by one in this step. The requirements of an algorithm that is both self-stabilizing and copes with napping faults are called $adjustment$ and $agreement$. We require that a fixed integer $k$ exists such that, starting in an arbitrary configuration, once a processor $P_i$ has been working correctly for at least $k$ time units and as long as $P_i$ continues to work, the following $adjustment$ and $agreement$ properties hold:

- $adjustment$: $P_i$ does not adjust its clock, and
- $agreement$: $P_i$'s clock agrees with the clock of every other processor that has also been working correctly for at least $k$ time units

如果在某个步骤中时钟值没有增加一，则处理器 $P_i$ 会在该步骤调整其时钟。一种既自稳定又能应对打盹故障的算法的要求称为 $adjustment$ 和 $agreement$。我们要求存在一个固定的整数 $k$，使得从任意配置开始，一旦处理器 $P_i$ 至少正确工作了 $k$ 个时间单位，并且只要 $P_i$ 继续工作，以下 $adjustment$ 和 $agreement$ 性质成立：

- $adjustment$：$P_i$ 不调整其时钟，
- $agreement$：$P_i$ 的时钟与其他所有至少正确工作了 $k$ 个时间单位的处理器的时钟一致。

Because a working processor must synchronize its clock in a fixed amount of time regardless of the actions of the other processors and because the system can be started in an arbitrary configuration, such algorithms are called *wait-free self-stabilizing* algorithms.

因为一个工作处理器必须在固定时间内同步其时钟，而不管其他处理器的操作如何，并且因为系统可以从任意配置启动，所以这样的算法称为 *无等待自稳定* 算法。

Note that trivial algorithms that fulfill only one of the above requirements exist. An algorithm in which a processor increments its clock by one in every pulse without communicating with its neighbors fulfills the adjustment requirement, and an algorithm in which the clock value of every processor is always 0 fulfills the agreement requirement.

Does an algorithm that fulfills both requirements exist? We first present a simple *wait-free self-stabilizing* algorithm with $k=1$ that uses **unbounded** clocks: a (non-crashed) processor reads the clocks of all the other processors in every step, chooses the maximal clock value in the system, denoted by $x$, and assigns $x+1$ to its clock.

请注意，存在只满足上述一个要求的简单算法。一种处理器在每个脉冲中将其时钟增加一而不与其邻居通信的算法满足调整要求，而一种每个处理器的时钟值始终为 0 的算法满足一致性要求。

那么，是否存在同时满足两个要求的算法？我们首先提出一个简单的具有 $k=1$ 的 *无等待自稳定* 算法，该算法使用**无限制**的时钟：每个（未崩溃的）处理器在每一步中读取所有其他处理器的时钟，选择系统中的最大时钟值，记为 $x$，然后将 $x+1$ 赋给其时钟。

Starting in an arbitrary configuration, immediately after an execution of a step by a processor $P_i$, the clock of $P_i$ holds the maximal clock value. Hence as long as $P_i$ does not crash, it does not adjust its clock; in other words, $P_i$ increments its clock by one in every pulse. Moreover, the clock of $P_i$ and every other processor that is not crashed hold the maximal clock value in the system. Thus, the clocks of all non-crashed processors that executed $k=1$ steps agree.

Does the algorithm tolerate transient faults as well as napping faults? Transient faults may cause the system to reach an arbitrary configuration $c$ with arbitrary clock values. Still, after this arbitrary configuration $c$, the clock of every processor $P_i$ holds the maximal clock value immediately after each of $P_i$'s steps.

从任意配置开始，处理器 $P_i$ 执行一步后，$P_i$ 的时钟持有最大时钟值。因此，只要 $P_i$ 不崩溃，它就不会调整其时钟；换句话说，$P_i$ 在每个脉冲中将其时钟增加一。此外，$P_i$ 的时钟和所有其他未崩溃的处理器的时钟都持有系统中的最大时钟值。因此，所有执行了 $k=1$ 步骤的未崩溃处理器的时钟是一致的。

算法是否能容忍瞬时故障以及打盹故障？瞬时故障可能导致系统达到具有任意时钟值的任意配置 $c$。然而，在这个任意配置 $c$ 之后，每个处理器 $P_i$ 在每次步骤执行后立即持有最大时钟值。

At this point, we already know that self-stabilizing algorithms that use bounded memory are more attractive than self-stabilizing algorithms that use unbounded memory. One might suggest a similar technique for bounded clocks. Instead of incrementing the clock values by one, an identical algorithm that increments the clock values by one modulo some integer $M$ might be used. Unfortunately, this suggestion will not work, since the clock of a napping processor that holds $M-1$ may cause all the active processors repeatedly to assign zero to their clocks and to violate the adjustment requirement for every possible integer $k$.

此时，我们已经知道，使用有限内存的自稳定算法比使用无限内存的自稳定算法更具吸引力。有人可能会建议使用类似技术来实现有限时钟。与其将时钟值增加一，可以使用一种相同的算法，通过模某个整数 $M$ 将时钟值增加一。不幸的是，这种建议不起作用，因为持有 $M-1$ 的休眠处理器可能导致所有活动处理器反复将其时钟赋值为零，从而违反每个可能整数 $k$ 的调整要求。

We now present a technique for a wait-free self-stabilizing clock synchronization algorithm that uses **a bounded clock value $M$ and a bounded number of states for a processor**. The idea is to use a mechanism for identifying crashed processors and ignoring their clock value.

Every two processors $P_i$ and $P_j$ have an "arrow" indicating which of them was recently active. In every step it executes, $P_i$ makes sure that it is not *behind* $P_j$. Using the analogy of the arrow, $P_i$ makes sure that **it is not the tail of the arrow by trying to direct the arrow toward itself**, while $P_j$ does the same from the other side. Immediately after both processors execute a step, the arrow has no direction; i.e., $P_i$ is not behind $P_j$ and $P_j$ is not behind $P_i$.

我们现在提出一种无等待自稳定时钟同步算法的技术，该算法使用**有限的时钟值 $M$ 和有限数量的处理器状态**。这个想法是使用一种机制来识别崩溃的处理器并忽略它们的时钟值。

每两个处理器 $P_i$ 和 $P_j$ 都有一个“箭头”指示它们中哪个最近处于活动状态。在每一步中，$P_i$ 确保它不落后于 $P_j$。使用箭头来类比，$P_i$ 通过**尝试将箭头指向自己来确保它不是箭头的尾部**，而 $P_j$ 则从另一侧做同样的事情。在两个处理器执行一步后，箭头没有方向；即 $P_i$ 不落后于 $P_j$，$P_j$ 也不落后于 $P_i$。

Interestingly, there is a simple self-stabilizing implementation of this arrow. The implementation is based on the concept of the famous *scissors*, *paper*, *stone* children’s game in which two children randomly choose an element in the set scissors, paper and stone. Every time they chose different elements a winner is declared according to the following rules: it is possible to wrap a stone with paper, to break scissors with a stone, and to cut paper with scissors.

有趣的是，这个箭头有一个简单的自稳定实现。这个实现基于著名的儿童游戏“剪刀、石头、布”的概念，其中两个孩子随机选择剪刀、石头和布集合中的一个元素。每次他们选择不同的元素时，会根据以下规则宣布获胜者：可以用布包住石头，可以用石头砸碎剪刀，可以用剪刀剪开布。

Each processor $P_i$ has an ${order}_{ij}$ variable for each neighbor $P_j$ that holds a value from {0, 1, 2}. $P_i$ writes in ${order}_{ij}$ and every processor can read ${order}_{ij}$.

The arrow has no direction when the value of ${order}_{ij}$ is equal to the value of ${order}_{ji}$.

When ${order}_{ij}$ is not equal to ${order}_{ji}$, the arrow is directed. In this case, we define the **behind** relation by the missing value $x$, which is the value from {0, 1, 2} such that $x = {order}_{ij}$ and $x = {order}_{ji}$. The convention we use is that **the value that follows $x$ in the clockwise direction is behind the other value**. In other words, $P_i$ is behind $P_j$ if and only if $({order}_{ij} + 1) \mod 3 = {order}_{ji}$.

每个处理器 $P_i$ 对每个邻居 $P_j$ 有一个 ${order}_{ij}$ 变量，取值范围为 {0, 1, 2}。$P_i$ 在 ${order}_{ij}$ 中写入值，每个处理器都可以读取 ${order}_{ij}$。

当 ${order}_{ij}$ 的值等于 ${order}_{ji}$ 的值时，箭头没有方向。

当 ${order}_{ij}$ 不等于 ${order}_{ji}$ 时，箭头有方向。在这种情况下，我们通过缺失值 $x$ 定义**落后**关系，$x$ 是 {0, 1, 2} 中的值，使得 $x = {order}_{ij}$ 和 $x = {order}_{ji}$。我们使用的约定是，**顺时针方向跟随 $x$ 的值落后于另一个值**。换句话说，当且仅当 $({order}_{ij} + 1) \mod 3 = {order}_{ji}$ 时，$P_i$ 落后于 $P_j$。

$P_i$ identifies all napping processors $P_j$ by incrementing $({order}_{ij} + 1) \mod 3$ as long as $P_j$ is not behind $P_i$. To get a clearer understanding of this mechanism, we observe the steps of two processors $P_i$ and $P_j$ in a specific execution.

This execution starts in a configuration in which the arrow has no direction and continues with a step of $P_i$ and $P_j$. In each step, both processors try to signal each other that they are executing a step by directing the arrow toward themselves.

Thus, $P_i$ increments ${order}_{ij}$ by 1 modulo 3 and $P_j$ does the same. This results in a configuration in which the arrow is not directed. The same holds as long as both processors continue to execute steps.

Immediately after the first time in which only one of the two processors, say $P_i$, executes a step, $P_j$ is behind $P_i$.

- When $P_j$ resumes operation, it finds that it is behind $P_i$ and increments ${order}_{ji}$ by 1 modulo 3.
- Note that, if $P_i$ executes a step, too, $P_i$ does not increment ${order}_{ij}$ since $P_j$ is behind $P_i$.

$P_i$ 通过递增 $({order}_{ij} + 1) \mod 3$ 来识别所有正在“打盹”的处理器 $P_j$，只要 $P_j$ 没有落后于 $P_i$。为了更清楚地理解这种机制，我们观察两个处理器 $P_i$ 和 $P_j$ 在特定执行过程中的步骤。

这个执行过程从一个箭头没有方向的配置开始，并以 $P_i$ 和 $P_j$ 的一步开始继续执行。在每一步中，两个处理器都试图通过将箭头指向自己来向对方发出信号，表明他们正在执行一个步骤。

因此，$P_i$ 通过 3 的模数递增 ${order}_{ij}$ 1 次，$P_j$ 也同样递增。这导致了一个箭头没有方向的配置。只要两个处理器继续执行步骤，这种情况就会保持不变。

第一次只有两个处理器中的一个（设为 $P_i$）执行步骤之后，$P_j$ 就落后于 $P_i$。

- 当 $P_j$ 恢复操作时，它发现自己落后于 $P_i$ 并以 3 的模数递增 ${order}_{ji}$ 1 次。
- 注意，如果 $P_i$ 也执行步骤，那么 $P_i$ 将不会递增 ${order}_{ij}$，因为 $P_j$ 落后于 $P_i$。

This order mechanism is used for selecting (bounded) clock values. The clock values of the napping processors are ignored. At each pulse, $P_i$ reads the order variables between every two processors $P_j$ and $P_l$, $1 \leq j$, $l \leq n$ in the system. Let $\mathcal{NB}$ be the set of processors that are not behind any other processor according to the order variables $P_i$ read.

- If $\mathcal{NB}$ is not empty, then $P_i$ chooses the maximal clock value $x$ among the clock values of the processors in $\mathcal{NB}$ and assigns $(x + 1) \mod M$ to its clock.
- Otherwise, $P_i$ does not change its clock value.

这种 order 的机制用于选择（有界的）时钟值。正在“打盹”的处理器的时钟值会被忽略。在每一次脉冲中，$P_i$ 会读取系统中每两个处理器 $P_j$ 和 $P_l$ 之间的 order 变量，$1 \leq j, l \leq n$。令 $\mathcal{NB}$ 为根据 $P_i$ 读取的 order 变量未落后于任何其他处理器的处理器集合。

- 如果 $\mathcal{NB}$ 非空，则 $P_i$ 选择 $\mathcal{NB}$ 内处理器的最大时钟值 $x$ 并将 $(x+1) \mod M$ 赋值给它的时钟。
- 否则，$P_i$ 不改变它的时钟值。

---

> THEOREM 6.1: The above algorithm is a wait-free self-stabilizing clock-synchronization algorithm with $k=2$.
>
> 定理 6.1：上述算法是一个参数 $k=2$ 的无等待自稳定时钟同步算法。

*Proof:*

The proof shows that, starting with any values in the $order$ variables and the $clock$ variables, the algorithm meets the adjustment and agreement requirements.

First note that all processors that take a step at the same pulse, see the same view, and compute the same $\mathcal{NB}$. We must show that, if any processor $P_i$ executes more than $k=2$ successive steps, then the agreement and adjustment requirements hold following its second step.

Assume $P_i$ executes more than $k$ successive steps. Our first observation is that $\mathcal{NB}$ is not empty following the first step of $P_i$. Moreover, while $P_i$ continues to execute steps without stopping, it remains in $\mathcal{NB}$. The reason is that $P_i$ executes a step in which it increments every order variable ${order}_{ij}$ such that $P_j$ is not behind $P_i$.

Since $\mathcal{NB}$ is not empty following the first step of $P_i$, and since all processors that execute a step see the same set $\mathcal{NB}$, all the processors that execute a step following the first step of $P_i$ choose the same clock value. Thus, following the second step of $P_i$ and while $P_i$ does not stop to execute steps, the clock values of the processors that belong to $\mathcal{NB}$  are the same.

Every processor that executes a single step belongs to $\mathcal{NB}$, and the value of the clocks of all processors in $\mathcal{NB}$ is the same; thus the agreement requirement holds. Every processor chooses the maximal clock value $M$ of a processor in $\mathcal{NB}$ and increments $M$ by 1 modulo $M$; thus, the adjustment requirement holds as well.

The proof of the theorem assumes an arbitrary starting configuration for the execution with any combination of $order$ and $clock$ values. Thus our algorithm is both wait-free and self-stabilizing. (End)

*证明：*

证明表明，从任意 $order$ 变量和 $clock$ 变量的值开始，该算法满足 $adjustment$ 和 $agreement$ 要求。

首先注意，在同一脉冲上采取步骤的所有处理器都会看到相同的视图，并计算相同的 $\mathcal{NB}$。我们必须证明，如果任何处理器 $P_i$ 执行超过 $k=2$ 个连续步骤，则在其第二步之后， $adjustment$ 和 $agreement$ 要求得到满足。

假设 $P_i$ 执行超过 $k$ 个连续步骤。我们的第一个观察结果是，在 $P_i$ 的第一步之后，$\mathcal{NB}$ 非空。此外，当 $P_i$ 连续执行步骤而没有停止时，它始终在 $\mathcal{NB}$ 中。原因是 $P_i$ 在执行的每一步中都会递增每个 ${order}_{ij}$ 变量，使得 $P_j$ 不落后于 $P_i$。

由于在 $P_i$ 的第一步之后 $\mathcal{NB}$ 非空，并且所有执行步骤的处理器都看到了相同的集合 $\mathcal{NB}$，因此在 $P_i$ 的第一步之后执行步骤的所有处理器都会选择相同的时钟值。因此，在 $P_i$ 的第二步之后并且在 $P_i$ 不停止执行步骤时，属于 $\mathcal{NB}$ 的处理器的时钟值是相同的。

每个执行单一步骤的处理器都属于 $\mathcal{NB}$，并且 $\mathcal{NB}$ 中所有处理器的时钟值相同；因此，一致性要求得到满足。每个处理器选择 $\mathcal{NB}$ 中处理器的最大时钟值 $M$ 并按 $M$ 的模数递增 1；因此，调整要求也得到了满足。

该定理的证明假设执行的起始配置是任意的，具有任何组合的 $order$ 和 $clock$ 值。因此，我们的算法既是无等待的又是自稳定的。(完)
