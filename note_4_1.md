# note_4_1_模型转换

包括 4.1、4.2 部分以及 PPT 内容。

> Topic: Converting Shared Memory to Message Passing (Ch. 4.2) This item is significantly shortened this year. For some theorems, we explain the meaning of their statement but skip their proofs.

跳过了相当多的证明。

<!-- TOC -->

- [note\_4\_1\_模型转换](#note_4_1_模型转换)
  - [4.1 Token-Passing](#41-token-passing)
  - [4.2 Data-Link Algorithms](#42-data-link-algorithms)
    - [背景](#背景)
    - [定义（$TP$ 任务）](#定义tp-任务)
    - [Unbounded Solution](#unbounded-solution)
    - [Lower Bound](#lower-bound)
    - [Bounded-Link and Randomized Solutions](#bounded-link-and-randomized-solutions)
      - [Bounded-Link Solutions](#bounded-link-solutions)
      - [Randomized Solutions](#randomized-solutions)
    - [Self-Stabilizing Simulation of Shared Memory](#self-stabilizing-simulation-of-shared-memory)

<!-- /TOC -->

## 4.1 Token-Passing

Converting a Central Daemon to read/write

> An algorithm designed to work in read/write atomicity can be used in any system in which there exists a central or a distributed daemon, while an algorithm designed for a system with a central or a distributed daemon cannot be used in a system that supports only read/write atomicity.

设计用于读/写原子性的算法可以在存在中央或分布式守护进程的任何系统中使用，而设计用于具有中央或分布式守护进程的系统的算法不能在仅支持读/写原子性的系统中使用。

## 4.2 Data-Link Algorithms

Converting Shared Memory to Message Passing

### 背景

Designing a self-stabilization algorithm for asynchronous message-passing systems is more subtle than the same task in asynchronous shared memory systems.

Main challenge: The messages stored in the communication links

- No bound on the message delivery time
- No bound on the number of messages that can be in link

Thus, **there are infinitely many initial configurations from which the system must stabilize**.

### 定义（$TP$ 任务）

Data-Link Algorithm: Messages fetched by sender from network layer should be delivered by receiver to network layer without duplications, omissions or reordering

One of the implementations of data-link task is the **token-passing algorithm**.

Token-passing task is a set of executions $TP$.

The **legal execution** of $TP$ is the sequence of configurations in which:

- No more than one processor holds the token.
- Both the sender and receiver hold the token in infinitely many configurations.

### Unbounded Solution

这个算法的主要内容和讲解（过程，达到安全配置）在书和 PPT 上都能看到。实际上都不难。几个重点：

- timeout mechanism: ensure that the system will not enter to *communication-deadlock* state.
- Each message has integer label called $MsgCounter$.
- Sender (and Receiver) maintains an **unbounded** local variable called $counter$.

引理和定理：

> LEMMA 4.1: A configuration $c$ in which all the counter variables of the messages and the processors are equal is a safe configuration for $TP$ and the algorithm in figure 4.1.
>
> 引理 4.1：所有消息和处理器的计数器变量相等的配置 $c$ 是 $TP$ 和图 4.1 中算法的安全配置。
>
> THEOREM 4.1: For every possible configuration $c$, every fair execution that starts in $c$ reaches a safe configuration with relation to $TP$.
>
> 定理 4.1：对于每个可能的配置 $c$，每个从 $c$ 开始的公平执行都达到与 $TP$ 相关的安全配置。

最后是对无界的提问：Whether bounded counters can replace the algorithm’s unbounded counter and label?

答案是否定的。假设了异步，以及无限的管道，那么就必须有无限的计数器。

但是这一点在实现上却有隐忧：这意味着算法需要使用的内存也是无限的。并且已证明了系统的内存必须以对数速率增长。

### Lower Bound

内存定义（实际来自于上一小节末尾）：

> The memory of the system is the number of bits required to encode the state of the sender, the state of the receiver, and the messages in transit (including the messages in the incoming and outgoing buffers of the links).

弱排他任务 $WE$（Weak-Exclusion task）：（PPT 上表述，和书上略有不同）

> In every legal execution $E$, there exists a combination of steps, a step for each processor, so that these steps are never executed concurrently.

我们接下来证明，在 $WE$ 任务上，系统所需内存也是无限的。

然后就跳过了证明。

> **Theorem:** For any self-stabilizing message driven protocol for $WE$ task and for any execution $E'$ in $WE$ all the configurations are **distinct**.
>
> Hence for any $t > 0$, the size of at least one of the first $t$ configurations in $E$ is at least $\log 2(t)$.

最后的一个结论：

> The implication of the above result is that no token-passing or stop-and-wait data-link algorithm exists with bounded memory resources. The memory of at least one of the system components must grow, either the memory of a processor or the number of bits in a message queue. In the next section we propose ways to cope in practice with the implications of the lower bound.
>
> 上述结果的含义是不存在具有有限内存资源的令牌传递或停等数据链路算法。系统组件中的至少一个的内存必须增长，要么是处理器的内存，要么是消息队列中的位数。在下一节中，我们提出了应对下界影响的实际方法。

### Bounded-Link and Randomized Solutions

看标题。是两种解决方案：

#### Bounded-Link Solutions

> In light of the lower bound above, it is impossible to devise a self-stabilizing bounded solution for the token-passing task in a completely asynchronous system, where the number of messages in transit is not bounded. In this section we relax the assumptions on asynchronous systems to obtain a bounded self-stabilizing algorithm for the token-passing task $TP$. For our first algorithm, we assume that **a bound on the number of messages in transit is known**.
>
> 鉴于上述下界的说明，在完全异步系统中设计一个自稳定的有界令牌传递任务解决方案是不可能的，其中在途消息的数量是无界的。在本节中，我们放宽了对异步系统的假设，以获得一个有界的自稳定令牌传递任务 $TP$ 的算法。对于我们的第一个算法，我们**假设已知在途消息的数量上限**。

注意，现在终于放宽条件了！

---

- Let $cap$ be the bound on the number of the messages in transit.
- The algorithm is the **same** as presented before with $counter$ incremented modulo $cap+1$.

  ```pseudocode
  counter := (MsgCounter + 1)mod(cap+1)
  ```

- Sender must **eventually** introduce a counter value that not existing in any message in transit.

#### Randomized Solutions

- The algorithm is the same as original one with $counter$ chosen **randomly**.

```pseudocode
label := ChooseLabel(MsgLabel)
```

- At least three labels should be used, {0, 1, 2}.
- The sender repeatedly send a message with particular label $l$ until a message with the same label $l$ arrives.
- The sender chooses randomly the next label $l'$ from the remaining labels so that $l' \neq l$.

### Self-Stabilizing Simulation of Shared Memory

- The heart of the simulation is a self-stabilizing implementation of the **read and write** operations.
- The simulation implements these operations by using a self-stabilizing, token passing algorithm.
- The algorithm run on the two links connecting any pair of neighbors.
- In each link the processor with the larger *ID* acts as the sender while the other as the receiver
  - NB: all processors have distinct *ID*s

---

- Register $r_{ij}$ is emulated using the variable of $r_{ij}$.
- Every time $p_i$ receives a token from $p_j$.
  - $p_i$ writes the current value of $r_{ij}$ to the token’s payload.
- **Write** operation of $p_i$ into $r_{ij}$ is implemented by locally writing into $r_{ij}$.
- **Read** operation of $p_i$ from $r_{ji}$ is implemented by:
  1. $p_i$ receives the token from $p_j$.
  2. $p_i$ receives, **again**, the token from $p_j$.
  3. Return the value attached to this token.

读写过程 PPT 中有展示。
