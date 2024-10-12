# 4.2 Data-Link Algorithms: Converting Shared Memory to Message Passing

<!-- TOC -->

- [4.2 Data-Link Algorithms: Converting Shared Memory to Message Passing](#42-data-link-algorithms-converting-shared-memory-to-message-passing)
  - [Unbounded Solution](#unbounded-solution)
  - [Lower Bound](#lower-bound)
  - [Bounded-Link and Randomized Solutions](#bounded-link-and-randomized-solutions)
  - [Self-Stabilizing Simulation of Shared Memory](#self-stabilizing-simulation-of-shared-memory)

<!-- /TOC -->

In the study of fault-tolerant message passing systems, it is customarily assumed that messages may be corrupted over links; hence, processors may enter arbitrary states and link contents may be arbitrary. Self-stabilizing algorithms handle these problems as well, since they are designed to recover from inconsistent global states.

在容错消息传递系统的研究中，通常假设消息可能在链路上传输时被破坏；因此，处理器可能进入任意状态，链路内容也可能是任意的。自稳定算法也能处理这些问题，因为它们设计用于从不一致的全局状态中恢复。

**Designing a self-stabilizing algorithm for asynchronous message-passing systems can be even more subtle than designing a self-stabilizing algorithm for a system that supports read/write atomicity**. The main difficulty is the messages stored in the communication links. A self-stabilizing system must cope with any possible initial state. When modeling asynchronous systems, **it is commonly assumed that there is no bound on message delivery time, and the number of messages that can be in (transit and in the buffers of) a link is also unbounded**. Thus, **there are infinitely many initial configurations from which the system must stabilize**. Again, one would like to design a self-stabilizing algorithm for the read/write atomicity model (or even the central daemon model) and then use a compiler that converts this algorithm into a self-stabilizing algorithm in the message-passing model. Our first goal in the design of such a compiler is a self-stabilizing data-link algorithm or token-passing algorithm. A self-stabilizing data-link algorithm will ensure that the unknown contents of the link are controlled, eliminating undesired messages from the link.

**为异步消息传递系统设计自稳定算法比为支持读/写原子性的系统设计自稳定算法更为微妙**。主要困难在于存储在通信链路中的消息。自稳定系统必须应对任何可能的初始状态。在建模异步系统时，**通常假设消息传递时间没有上限，并且链路中（在传输和缓冲区中的）消息数量也是无限的**。因此，**系统必须从无数的初始配置中稳定下来**。同样，人们希望为读/写原子性模型（甚至是中央守护进程模型）设计自稳定算法，然后使用编译器将该算法转换为消息传递模型中的自稳定算法。我们在设计这种编译器时的第一个目标是自稳定数据链路算法或令牌传递算法。自稳定数据链路算法将确保链路中未知的内容受到控制，消除链路中的不需要的消息。

Recall that the data-link algorithm is designed for a system of two processors, one of which is the sender and the other the receiver. The task of the data-link algorithm is to use retransmissions in order to deliver messages from the sender to the receiver. The messages that have to be delivered are given to the sender by a higher-level algorithm of the network layer. The messages that are fetched by the sender from the network layer (at the sender side) should be delivered by the receiver to the network layer (at the receiver side) without duplications, omissions, or reordering. A message $m$ that is fetched by the sender from the network layer is sent repeatedly as part of (messages called) frames to the receiver until the sender receives an indication that $m$ was delivered by the receiver to the network layer (on the receiver side).

回想一下，数据链路算法是为两个处理器的系统设计的，其中一个是发送方，另一个是接收方。数据链路算法的任务是通过重传将消息从发送方传递到接收方。需要传递的消息由网络层的高级算法提供给发送方。发送方从网络层（在发送方一侧）获取的消息应由接收方传递到网络层（在接收方一侧），且不应有重复、遗漏或重新排序。发送方从网络层获取的消息 $m$ 将作为帧的一部分反复发送给接收方，直到发送方收到接收方已将 $m$ 传递到网络层（在接收方一侧）的指示为止。

For instance, in the *stop-and-wait* data-link algorithm, each message sent by the sender is acknowledged by the receiver before the next message is sent. This behavior is similar to that of a token-passing algorithm, in which a single token is repeatedly sent from the sender to the receiver and back. First note that a stop-and-wait algorithm implements the token-passing task: each time the sender decides to fetch a new message, a token is present at the sender side; each time the receiver decides to deliver a message, a token is present at the receiver side. On the other hand, a token-passing algorithm implements the data-link task: the token is transferred from the sender to the receiver and back by sending frames between the sender and the receiver. Therefore, roughly speaking, the content of every message that the sender fetches can be attached to every frame sent for transferring the token. A new message is fetched whenever a token arrives at the sender. Once the receiver decides that a token has arrived, it delivers the message that is attached to the last arriving frame. Note that, in both these tasks, it is impossible for the sender and the receiver to hold the token (or fetch and deliver messages) simultaneously. In other words, there is no configuration in which there are two applicable steps $a_1$ and $a_2$ such that the sender sends a token (fetches a message) during $a_1$ and the receiver receives a token (delivers a message, respectively) during $a_2$.

例如，在 *停等* 数据链路算法中，发送方发送的每条消息在发送下一条消息之前都会由接收方确认。这种行为类似于令牌传递算法，其中单个令牌在发送方和接收方之间反复传递。首先注意，停等算法实现了令牌传递任务：每次发送方决定获取新消息时，令牌存在于发送方；每次接收方决定传递消息时，令牌存在于接收方。另一方面，令牌传递算法实现了数据链路任务：通过在发送方和接收方之间发送帧，令牌在发送方和接收方之间传递。因此，大致来说，发送方获取的每条消息的内容可以附加到每个用于传递令牌的帧上。每当令牌到达发送方时，就会获取新消息。一旦接收方决定令牌已到达，它就会传递附加到最后到达帧上的消息。注意，在这两种任务中，发送方和接收方不可能同时持有令牌（或同时获取和传递消息）。换句话说，不存在一种配置，其中有两个适用的步骤 $a_1$ 和 $a_2$，使得发送方在 $a_1$ 期间发送令牌（获取消息），而接收方在 $a_2$ 期间接收令牌（分别传递消息）。

**We abstract the requirements of the data-link algorithm and the token-passing algorithm tasks in a set of executions $TP$. The task $TP$ of legitimate sequences is defined as the set of all configuration sequences in which no more than one processor holds the token and both the sender and the receiver hold the token in infinitely many configurations in every sequence in $TP$**. In what follows we consider the $TP$ task. Thus, in the next three sections there is no need to use both the terms message and frame — and hence we use only the term message.

**我们将数据链路算法和令牌传递算法任务的要求抽象为一组执行 $TP$。合法序列任务 $TP$ 定义为所有配置序列的集合，其中没有一个处理器持有令牌，并且在 $TP$ 中的每个序列中，发送方和接收方在无限多的配置中都持有令牌**。在下文中，我们考虑 $TP$ 任务。因此，在接下来的三节中，不需要同时使用消息和帧这两个术语——因此我们只使用消息这个术语。

## Unbounded Solution

The first algorithm we propose uses an unbounded counter to achieve the $TP$ task. A timeout mechanism is assumed to ensure that the system is not started in a *communication-deadlock configuration* in which there is no message in transit in the communication links and neither the sender nor the receiver is about to send a message.

我们提出的第一个算法使用一个无界计数器来实现 $TP$ 任务。假设有一个超时机制，以确保系统不会在 *通信死锁配置* 中启动，即通信链路中没有消息在传输，发送方和接收方都没有要发送的消息。

The code for this algorithm appears in figure 4.1. In this algorithm, each message has an integer label called $MsgCounter$ and both the sender and the receiver maintain a variable called $counter$. The sender repeatedly sends the value of its counter until it receives a message from the receiver with the same value. Once such a message arrives at the sender, the sender receives the token, increments the value of the counter and sends the new counter value in a message to the receiver. When the new counter is sent, the sender stops holding the token. For every message $m$ received by the receiver, the receiver sends a message $m'$ with a label identical to the label of $m$. In addition, the receiver stores in its counter variable the last counter value received in a message. Every time a message with a value different from the counter value of the receiver arrives at the receiver, the receiver receives the token, updates its counter value, and sends the message back to the sender. When this message is sent, the receiver stops holding the token.

该算法的代码如图 4.1 所示。在这个算法中，每条消息都有一个称为 $MsgCounter$ 的整数标签，发送方和接收方都维护一个称为 $counter$ 的变量。发送方反复发送其计数器的值，直到收到来自接收方的具有相同值的消息。一旦这样的消息到达发送方，发送方就会接收令牌，增加计数器的值，并将新的计数器值发送给接收方。当新的计数器值被发送时，发送方停止持有令牌。对于接收方收到的每条消息 $m$，接收方发送一条标签与 $m$ 的标签相同的消息 $m'$。此外，接收方在其计数器变量中存储最后收到的消息中的计数器值。每当一条消息的值与接收方的计数器值不同的消息到达接收方时，接收方接收令牌，更新其计数器值，并将消息发送回发送方。当这条消息被发送时，接收方停止持有令牌。

Recall that a configuration of the system is a tuple $c = (s_s, s_r, q_{s,r}, q_{r,s})$, where $s_s$ ($s_r$) is the state of the sender (the receiver, respectively) and $q_{s,r}$ ($q_{r,s}$) is the queue of messages sent by the sender to the receiver (by the receiver to the sender, respectively) but not yet received. A computation step $a$ of a processor starts with a single communication operation, message send or message receive, then continues with internal computations, and ends just before the next communication operation. A computation step of the sender can also be started by a *timeout mechanism* instead of message arrival. The timeout mechanism is designed to cope with message loss. A ${timeout}_s$ event is an environment step (similar to $loss_{i,j}(m)$) that is executed whenever the system reaches a communication deadlock configuration. For example, a deadlock configuration can be reached due to message loss that eliminates all messages in transit while both the sender and the receiver are waiting to receive a message. An execution $E = (c_1, a_1, c_2, a_2, ···)$ is an alternating sequence of configurations and steps such that $c_{i−1} \xrightarrow{a_{i−1}} c_i$ ($i > 1$).

回想一下，系统的配置是一个元组 $c = (s_s, s_r, q_{s,r}, q_{r,s})$，其中 $s_s$ ($s_r$) 是发送方（接收方）的状态，$q_{s,r}$ ($q_{r,s}$) 是发送方发送给接收方（接收方发送给发送方）但尚未接收的消息队列。处理器的计算步骤 $a$ 以单个通信操作（消息发送或消息接收）开始，然后继续内部计算，并在下一个通信操作之前结束。发送方的计算步骤也可以由 *超时机制* 而不是消息到达启动。超时机制旨在应对消息丢失。一个 ${timeout}_s$ 事件是一个环境步骤（类似于 $loss_{i,j}(m)$），在系统达到通信死锁配置时执行。例如，由于消息丢失导致所有在传输中的消息被消除，而发送方和接收方都在等待接收消息，这样就会达到死锁配置。一个执行 $E = (c_1, a_1, c_2, a_2, ···)$ 是配置和步骤的交替序列，使得 $c_{i−1} \xrightarrow{a_{i−1}} c_i$ ($i > 1$)。

![figure_4.1](images/figure_4.1.png)

A safe configuration for $TP$ and the algorithm in figure 4.1 is a configuration in which the counter values of all the messages in $q_{s,r}$ and $q_{r,s}$, as well as the values of the counters maintained by the sender and the receiver, have the same value. Interestingly, the correctness proof of the first self-stabilizing datalink algorithm resembles the correctness proof of Dijkstra's mutual-exclusion algorithm presented in section 2.6. According to our definition, the $ME$ task and the $TP$ task are identical. Still in some cases (e.g., section 8.1), we use the $TP$ task to transfer information together with the token. The following lemma proves that, indeed, every fair execution that starts with such a safe configuration belongs to $TP$.

对于 $TP$ 和图 4.1 中的算法来说，一个安全配置是指 $q_{s,r}$ 和 $q_{r,s}$ 中所有消息的计数器值，以及发送方和接收方维护的计数器值都相同的配置。有趣的是，第一个自稳定数据链路算法的正确性证明类似于第 2.6 节中介绍的 Dijkstra 互斥算法的正确性证明。根据我们的定义，$ME$ 任务和 $TP$ 任务是相同的。然而在某些情况下（例如，第 8.1 节），我们使用 $TP$ 任务来传递信息和令牌。以下引理证明，确实，每个从这种安全配置开始的公平执行都属于 $TP$。

---

> LEMMA 4.1: A configuration $c$ in which all the counter variables of the messages and the processors are equal is a safe configuration for $TP$ and the algorithm in figure 4.1.
>
> 引理 4.1：所有消息和处理器的计数器变量相等的配置 $c$ 是 $TP$ 和图 4.1 中算法的安全配置。

*Proof:*

Following $c$, the sender must receive a message with a counter value that is equal to its counter variable, because such a message either exists already in one of the queues or is sent due to a timeout. Once the sender receives a message with a counter value that is equal to its counter, the sender has the token. Then the sender increments its counter value and sends a message with the new counter value (releasing the token). Before a message with the new counter value reaches the sender, it must reach the receiver. Therefore, the receiver holds a token before the sender holds the token again. Once the sender receives a message with the value of its counter, a new configuration in which the value of all the counters is identical is reached, and the same arguments can be applied. (End)

*证明：*

在配置 $c$ 之后，发送方必须接收到一个计数器值等于其计数器变量的消息，因为这样的消息要么已经存在于某个队列中，要么由于超时而被发送。一旦发送方接收到一个计数器值等于其计数器的消息，发送方就拥有了令牌。然后发送方增加其计数器值，并发送一个带有新计数器值的消息（释放令牌）。在带有新计数器值的消息到达发送方之前，它必须先到达接收方。因此，接收方在发送方再次持有令牌之前持有令牌。一旦发送方接收到一个计数器值等于其计数器的消息，就达到了一个所有计数器值相同的新配置，并且可以应用相同的论点。（完）

---

We are now ready to prove the main theorem.

我们现在准备证明主要定理。

---

> THEOREM 4.1: For every possible configuration $c$, every fair execution that starts in $c$ reaches a safe configuration with relation to $TP$.
>
> 定理 4.1：对于每个可能的配置 $c$，每个从 $c$ 开始的公平执行都达到与 $TP$ 相关的安全配置。

*Proof:*

First we claim that, in every fair execution, the value of the sender's counter is incremented infinitely often. Assume, by way of contradiction, that there exists a configuration after which the value of the sender's counter is not changed. Since the execution is fair, every computation step that is applicable infinitely often is executed; in particular, every message that is sent infinitely often is received. The timeout mechanism ensures that at least one message is sent repeatedly by the sender. This message is eventually received by the receiver, which forwards it back to the sender, causing the sender to increment its counter. We conclude that in every fair execution there is no configuration after which the sender does not increment its counter. Let $max$ be the maximal value of a counter in a message or in one of the counter variables in the first configuration of the execution $c$. When the sender assigns the value $max+1$ to its counter variable, the sender introduces a new counter value not existing in any previous configuration of the execution that starts in $c$. Thus, when the sender receives a message from the receiver with $MsgCounter = max+1$, the system is in a safe configuration. (End)

*证明：*

首先我们声称，在每个公平执行中，发送方的计数器值无限次增加。假设通过反证法，存在一个配置，在此之后发送方的计数器值不再变化。由于执行是公平的，每个无限次适用的计算步骤都会被执行；特别是，每个无限次发送的消息都会被接收。超时机制确保发送方至少重复发送一条消息。该消息最终被接收方接收，接收方将其转发回发送方，导致发送方增加其计数器。我们得出结论，在每个公平执行中，不存在发送方不增加其计数器的配置。设 $max$ 为执行 $c$ 的第一个配置中消息或计数器变量中的最大计数器值。当发送方将值 $max+1$ 分配给其计数器变量时，发送方引入了一个在从 $c$ 开始的执行的任何先前配置中不存在的新计数器值。因此，当发送方接收到来自接收方的 $MsgCounter = max+1$ 的消息时，系统处于安全配置中。（完）

---

A question to ask at this stage is **whether an algorithm that uses only bounded memory exists — in particular, whether the unbounded counter and label can be eliminated. We answer this question negatively**, proving that the memory of the system must grow at a logarithmic rate, where **the memory of the system is the number of bits required to encode the state of the sender, the state of the receiver, and the messages in transit (including the messages in the incoming and outgoing buffers of the links)**. The lower bound is proved on the *size* of the system configuration, where the size is the number of bits required to encode a configuration.

在这个阶段要问的一个问题是，**是否存在仅使用有限内存的算法——特别是，是否可以消除无界计数器和标签。我们对这个问题的回答是否定的**，证明了系统的内存必须以对数速率增长，其中**系统的内存是编码发送方状态、接收方状态和传输中的消息（包括链路的输入和输出缓冲区中的消息）所需的位数**。下限是在系统配置的 *大小* 上证明的，其中大小是编码一个配置所需的位数。

## Lower Bound

Before we present the lower bound, let us discuss issues concerning retransmission and timeouts. One can argue that there is no bound on the size of a configuration when a timeout mechanism is used. The number of messages in $q_{s,r}$ grows each time the timeout mechanism is invoked. Therefore, in an execution in which the sender sends a message at every step due to timeouts, the configuration size grows linearly with the number of steps of the sender. Recall that the timeout mechanism was introduced to cope with situations in which no message is in transit. Therefore, we choose to **assume that the time it takes for the timeout mechanism to be triggered is long enough to guarantee that, indeed, no message is in transit in the system**.

在我们提出下限之前，让我们讨论有关重传和超时的问题。可以认为，当使用超时机制时，配置的大小是没有上限的。每次调用超时机制时，$q_{s,r}$ 中的消息数量都会增加。因此，在每一步由于超时发送消息的执行中，配置大小随着发送方步骤的数量线性增长。回想一下，引入超时机制是为了应对没有消息在传输中的情况。因此，我们选择**假设触发超时机制所需的时间足够长，以保证系统中确实没有消息在传输**。

Can the assumption on the timeout mechanism be used by the algorithm designer? For example, one can propose an algorithm in which the sender waits every so often for a timeout, ignoring any arriving messages. It can be argued that such a technique can be used to eliminate pending messages with undesired labels from the system. However, as we show next, the timeout mechanism should be invoked repeatedly in order to ensure that a safe configuration is reached from every possible system state. This, in turn, implies a long delay in message transmission over the entire execution, not only during convergence. Moreover, it is possible that the timeout mechanism in certain systems does not guarantee that no message is in transit when a timeout is triggered. Therefore, one would prefer an algorithm that stabilizes without using an assumption on the contents of the links when a timeout occurs.

算法设计者可以利用对超时机制的假设吗？例如，可以提出一种算法，其中发送方每隔一段时间等待超时，忽略任何到达的消息。可以认为，这种技术可以用来消除系统中带有不需要标签的未决消息。然而，正如我们接下来所展示的，为了确保从每个可能的系统状态达到安全配置，超时机制应被反复调用。这反过来意味着整个执行过程中消息传输的长时间延迟，而不仅仅是在收敛期间。此外，在某些系统中，超时机制可能无法保证在触发超时时没有消息在传输。因此，人们更倾向于设计一种**在超时发生时不依赖链路内容假设的算法**。

The lower bound presented in this section is proved for an arbitrary *weak-exclusion* task $WE$. A task is in the weak-exclusion set of tasks if, in every legal execution $E$, there exists a combination of steps, a step for each processor (the sender and the receiver), such that both steps appear in $E$ but are never applicable simultaneously. In other words, **these steps are never executed concurrently**. The $TP$ task does not allow two simultaneous steps in which the sender and the receiver receive the token. To simplify the proof of the lower bound, we assume in what follows that every computation step starts with a message receive followed by local computation and ending with a message send. Note that such a scheduling policy results in a possible execution.

本节中提出的下限是针对任意 *弱排他* 任务 $WE$ 证明的。如果在每个合法执行 $E$ 中，存在一个步骤组合，每个处理器（发送方和接收方）都有一个步骤，这些步骤都出现在 $E$ 中但从未同时适用，那么任务属于弱排他任务集。换句话说，**这些步骤从不同时执行**。$TP$ 任务不允许发送方和接收方同时接收令牌。为了简化下限的证明，我们在下文中假设每个计算步骤以消息接收开始，接着是本地计算，并以消息发送结束。注意，这样的调度策略会导致可能的执行。

For any configuration $c$ and any directed link $(x, y)$ from $P_x$ to $P_y$ (where $P_x$ is the sender and $P_y$ is the receiver or vice versa), let $q_{x,y}(c)$ be the sequence of messages pending on the link from $P_x$ to $P_y$ in $c$. For any execution $E$, let ${qs}_{x,y}(E)$ be the sequence of messages sent by $P_x$ on the link leading to $P_y$ during $E$. Similarly, let ${qr}_{x,y}(E)$ be the sequence of messages received by $P_y$ from the link $(x, y)$ during $E$. We use the notation $p \circ q$ for the concatenation of $p$ and $q$.

对于任何配置 $c$ 和从 $P_x$ 到 $P_y$ 的任何有向链路 $(x, y)$（其中 $P_x$ 是发送方，$P_y$ 是接收方，反之亦然），令 $q_{x,y}(c)$ 为配置 $c$ 中从 $P_x$ 到 $P_y$ 的链路上未决消息的序列。对于任何执行 $E$，令 ${qs}_{x,y}(E)$ 为 $P_x$ 在执行 $E$ 期间在通向 $P_y$ 的链路上发送的消息序列。同样，令 ${qr}_{x,y}(E)$ 为 $P_y$ 在执行 $E$ 期间从链路 $(x, y)$ 接收的消息序列。我们使用符号 $p \circ q$ 表示 $p$ 和 $q$ 的连接。

---

> LEMMA 4.2: For every execution $E = (c_1, a_1,··· , a_k, c_{k+1})$ in which no message is lost, it holds that $q_{x,y}(c_1) \circ {qs}_{x,y}(E) = {qr}_{x,y}(E) \circ q_{x,y}(c_{k+1})$.
>
> 引理 4.2：对于每个执行 $E = (c_1, a_1,··· , a_k, c_{k+1})$，如果没有消息丢失，则有 $q_{x,y}(c_1) \circ {qs}_{x,y}(E) = {qr}_{x,y}(E) \circ q_{x,y}(c_{k+1})$。

*Proof:*

The left-hand side of the equation contains the messages on the link $(x, y)$ in $c_1$, concatenated with the messages sent during $E$ through $(x, y)$. The right-hand side of the equation contains the messages received during $E$ through $(x, y)$, concatenated with the messages left on $(x, y)$ in $c_{k+1}$. The proof is completed by a simple induction on $k$ to show that, if no message is lost during $E$, then both sides of the equation represent the same sequence of messages. (End)

*证明：*

方程左侧包含配置 $c_1$ 中链路 $(x, y)$ 上的消息，与在 $E$ 期间通过 $(x, y)$ 发送的消息连接在一起。方程右侧包含在 $E$ 期间通过 $(x, y)$ 接收的消息，与配置 $c_{k+1}$ 中留在 $(x, y)$ 上的消息连接在一起。通过对 $k$ 进行简单的归纳证明，如果在 $E$ 期间没有消息丢失，则方程两侧表示相同的消息序列。（完）

---

An execution $E = (c_1, a_1, \ldots, c_{\mathscr{l}-1}, a_{\mathscr{l}-1})$ whose result configuration $c_1$ is equal to its initial configuration $c_1$ is called a *circular execution*. Repeating a circular execution $E$ forever yields an infinite execution $E^\infty$. Observe that an execution in which a certain configuration appears more than once has a circular sub-execution $\overline E = (c_i, a_i, ···, a_{i+\mathscr{l}−1}, c_{i+\mathscr{l}})$ $\equiv$ $(\overline c_1, \overline a_1, ···, \overline a_{\mathscr{l}−1}, \overline c_\mathscr{l})$, where $c_i = c_{i+\mathscr{l}} = \overline c_1 = \overline c_\mathscr{l}$.

一个执行 $E = (c_1, a_1, \ldots, c_{\mathscr{l}-1}, a_{\mathscr{l}-1})$，其结果配置 $c_1$ 等于其初始配置 $c_1$，称为*循环执行*。无限重复一个循环执行 $E$ 会产生一个无限执行 $E^\infty$。注意，一个配置多次出现的执行具有一个循环子执行 $\overline E = (c_i, a_i, ···, a_{i+\mathscr{l}−1}, c_{i+\mathscr{l}})$ $\equiv$ $(\overline c_1, \overline a_1, ···, \overline a_{\mathscr{l}−1}, \overline c_\mathscr{l})$，其中 $c_i = c_{i+\mathscr{l}} = \overline c_1 = \overline c_\mathscr{l}$。

Let $\mathcal{AL}$ be an arbitrary self-stabilizing algorithm for a $WE$ task. To show that, in every execution of $\mathcal{AL}$,  all the configurations are distinct, we assume that $\mathcal{AL}$ has a circular sub-execution $\overline E$ and reach a contradiction by showing that $\mathcal{AL}$ is not self-stabilizing. Using $\overline E$, we now construct an initial configuration $c_{init}$ by changing the list of messages in transit on the system's links. For each link $(x, y)$, the list of messages in transit on $(x, y)$ at $c_{init}$ is obtained by concatenating the list of messages in transit on $(x, y)$ at $\overline c_1$ with the list of all messages sent on $(x, y)$ during $\overline E$.

设 $\mathcal{AL}$ 为一个任意的自稳定算法，用于 $WE$ 任务。为了证明在 $\mathcal{AL}$ 的每次执行中，所有配置都是不同的，我们假设 $\mathcal{AL}$ 有一个循环子执行 $\overline E$，并通过证明 $\mathcal{AL}$ 不是自稳定的来达到矛盾。使用 $\overline E$，我们现在通过改变系统链路上传输中的消息列表来构造一个初始配置 $c_{init}$。对于每个链路 $(x, y)$，$c_{init}$ 上 $(x, y)$ 上传输中的消息列表是通过将 $\overline c_1$ 上 $(x, y)$ 上传输中的消息列表与 $\overline E$ 期间在 $(x, y)$ 上发送的所有消息列表连接起来获得的。

Roughly speaking, the effect of this change is to create an additional "padding layer" of messages that helps to decouple each *send* from its counterpart *receive* and achieve additional flexibility in the system, and this which enables us to prove the lower bound. Formally, $c_{init}$ is obtained from $\overline c_1$ as follows:

- The state of each processor in $c_{init}$ is equal to its state in $\overline c_1$.
- For any link $(x, y)$, $q_{x,y}(c_{init}) = q_{x,y}(c_1) \circ {qs}_x,y(\overline E)$.

大致来说，这种变化的效果是创建一个额外的“填充层”消息，有助于将每个*发送*与其对应的*接收*分离开来，并在系统中实现额外的灵活性，这使我们能够证明下限。形式上，$c_{init}$ 是从 $\overline c_1$ 获得的，如下所示：

- $c_{init}$ 中每个处理器的状态等于其在 $\overline c_1$ 中的状态。
- 对于任何链路 $(x, y)$，$q_{x,y}(c_{init}) = q_{x,y}(c_1) \circ {qs}_{x,y}(\overline E)$。

Let $\mathcal{S}_x (\overline E)$ be the sequence of steps executed by $P_x$ during $\overline E$. Define $merge(\overline S)$ to be the set of sequences obtained by all possible merging of the sequences $\mathcal{S}_s(\overline E)$ and $\mathcal{S}_r (\overline E)$, while keeping the internal order in $\mathcal{S}_s (\overline E)$ and $\mathcal{S}_r (\overline E)$. Note that all the sequences in $merge(\overline S)$ have the same finite length and contain the same steps in different orders.

设 $\mathcal{S}_x (\overline E)$ 为 $P_x$ 在 $\overline E$ 期间执行的步骤序列。定义 $merge(\overline S)$ 为通过 $\mathcal{S}_s(\overline E)$ 和 $\mathcal{S}_r (\overline E)$ 序列的所有可能合并获得的序列集合，同时保持 $\mathcal{S}_s (\overline E)$ 和 $\mathcal{S}_r (\overline E)$ 的内部顺序。注意，$merge(\overline S)$ 中的所有序列具有相同的有限长度，并包含相同的步骤但顺序不同。

---

> LEMMA 4.3: Every $S \in merge(\overline S)$ is applicable to $c_{init}$, and the resulting execution is a circular execution of $\mathcal{AL}$.
>
> 引理 4.3：每个 $S \in merge(\overline S)$ 都适用于 $c_{init}$，并且得到的执行是 $\mathcal{AL}$ 的循环执行。

*Proof:*

Let $S$ be an arbitrary sequence in $merge(\overline S)$ and let $P_x$ be an arbitrary processor of the system. Then we have: (1) The initial state of $P_x$ in $c_{init}$ is equal to its initial state in $\overline c_1$. (2) In $c_{init}$, all messages that $P_x$ receives during $\overline E$ are stored in $P_x$ 's incoming link in the right order. (3) The steps of $P_x$ appear in $S$ in the same order as they appear in  $S_x(\overline E)$. (1) — (3) imply that the sequence $S$ is applicable to $c_{init}$, and the application of $S$ to $c_{init}$ yields an execution $E_S$ with result configuration $c_{res}$ whose state vector is equal to the state vector of $c_{init}$ and in which, for every link $(x, y)$, ${qs}_{x,y}(E_S) = {qs}_{x,y}(\overline E)$ and ${qr}_{x,y}(E_S) = {qr}_{x,y}(\overline E)$.

To prove that the execution obtained is circular, it remains to show that the content of every link in the result configuration $c_{res}$ is equal to its content in $c_{init}$: in other words, that $q_{x,y}(c_{init}) = q_{x,y}(c_{res})$. For every link $(x, y)$, it holds that:

1. $q_{x,y}(c_{init}) \circ {qs}_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(c_{res})$ (by lemma 4.2 above and the fact that ${qs}_{x,y}(E_S) = {qs}_{x,y}(\overline E)$ and ${qr}_{x,y}(E_S) = {qr}_{x,y}(\overline E)$).

2. $q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(\overline c_1)$ (by lemma 4.2 and the circularity of $\overline E$).

Replacing $q_{x,y}(c_{init})$ in equation 1 with its explicit contents yields:

3. $q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E) \circ {qs}_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(c_{res})$.

Using equation 2 to replace $q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E)$ by ${qr}_{x,y}(\overline E) \circ q_{x,y}(\overline c_1)$ in equation 3 gives:

4. ${qr}_{x,y}(\overline E) \circ q_{x,y}(\overline c_1) \circ q_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(c_{res})$.

Eliminating ${qr}_{x,y}(\overline E)$ from both sides of equation 4 yields the desired result: $q_{x,y}(c_{init}) = q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E) = q_{x,y}(c_{res})$, which proves the lemma. (End)

*证明：*

设 $S$ 为 $merge(\overline S)$ 中的任意序列，设 $P_x$ 为系统中的任意处理器。则我们有：(1) $c_{init}$ 中 $P_x$ 的初始状态等于其在 $\overline c_1$ 中的初始状态。(2) 在 $c_{init}$ 中，$P_x$ 在 $\overline E$ 期间接收的所有消息都按正确顺序存储在 $P_x$ 的输入链路中。(3) $P_x$ 的步骤在 $S$ 中的出现顺序与它们在 $S_x(\overline E)$ 中的出现顺序相同。(1) — (3) 意味着序列 $S$ 适用于 $c_{init}$，并且将 $S$ 应用于 $c_{init}$ 会产生一个执行 $E_S$，其结果配置 $c_{res}$ 的状态向量等于 $c_{init}$ 的状态向量，并且对于每个链路 $(x, y)$，${qs}_{x,y}(E_S) = {qs}_{x,y}(\overline E)$ 和 ${qr}_{x,y}(E_S) = {qr}_{x,y}(\overline E)$。

为了证明得到的执行是循环的，剩下的就是证明结果配置 $c_{res}$ 中每个链路的内容等于其在 $c_{init}$ 中的内容：换句话说，即 $q_{x,y}(c_{init}) = q_{x,y}(c_{res})$。对于每个链路 $(x, y)$，有：

1. $q_{x,y}(c_{init}) \circ {qs}_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(c_{res})$（根据上面的引理 4.2 以及 ${qs}_{x,y}(E_S) = {qs}_{x,y}(\overline E)$ 和 ${qr}_{x,y}(E_S) = {qr}_{x,y}(\overline E)$ 的事实）。

2. $q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(\overline c_1)$（根据引理 4.2 和 $\overline E$ 的循环性）。

在方程 1 中用其显式内容替换 $q_{x,y}(c_{init})$ 得到：

3. $q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E) \circ {qs}_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(c_{res})$。

使用方程 2 将方程 3 中的 $q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E)$ 替换为 ${qr}_{x,y}(\overline E) \circ q_{x,y}(\overline c_1)$ 得到：

4. ${qr}_{x,y}(\overline E) \circ q_{x,y}(\overline c_1) \circ q_{x,y}(\overline E) = {qr}_{x,y}(\overline E) \circ q_{x,y}(c_{res})$。

从方程 4 的两边消去 ${qr}_{x,y}(\overline E)$ 得到所需结果：$q_{x,y}(c_{init}) = q_{x,y}(\overline c_1) \circ {qs}_{x,y}(\overline E) = q_{x,y}(c_{res})$，这证明了该引理。（完）

---

Define $blowup(\overline E)$ as the set of executions whose initial state is $c_{init}$ and whose sequence of steps belongs to $merge(\overline S)$. Notice that, for every circular execution $\overline E$ and every execution $E \in blowup(\overline E)$, it holds that $\mathcal{S}_x(E) = \mathcal{S}_x(E)$.

定义 $blowup(\overline E)$ 为那些初始状态为 $c_{init}$ 且步骤序列属于 $merge(\overline S)$ 的执行集合。注意，对于每一个循环执行 $\overline E$ 和每一个执行 $E \in blowup(\overline E)$，都有 $\mathcal{S}_x(E) = \mathcal{S}_x(E)$。

---

> LEMMA 4.4: For any set of steps $B = \{a_1, a_2\}$, where $a_1 \in \mathcal{S}_s(\overline E)$ and $a_2 \in \mathcal{S}_r(\overline E)$, there is an execution $E \in blowup(\overline E)$ that contains a configuration for which the atomic steps in $B$ are concurrently applicable.
>
> 引理 4.4：对于任何步骤集合 $B = \{a_1, a_2\}$，其中 $a_1 \in \mathcal{S}_s(\overline E)$ 且 $a_2 \in \mathcal{S}_r(\overline E)$，存在一个执行 $E \in blowup(\overline E)$，其包含一个配置，在该配置中 $B$ 中的原子步骤是同时适用的。

*Proof:*

Let $S \in merge(\overline S)$ be the sequence constructed as follows: first take all the steps in $\mathcal{S}_s(\overline E)$ that precede $a_1$; then take all the steps in $\mathcal{S}_r(\overline E)$ that precede $a_2$. Applying the sequence constructed so far to $c_{init}$ results in a configuration in which both $a_1$ and $a_2$ are applicable. This sequence is completed to a sequence $S$ in $merge(\overline S)$ by taking the remaining steps in an arbitrary order that keeps the internal order of each $\mathcal{S}_x (\overline E)$. (End)

*证明：*

设 $S \in merge(\overline S)$ 为如下构造的序列：首先取 $\mathcal{S}_s(\overline E)$ 中所有在 $a_1$ 之前的步骤；然后取 $\mathcal{S}_r(\overline E)$ 中所有在 $a_2$ 之前的步骤。将构造的序列应用于 $c_{init}$，得到一个配置，在该配置中 $a_1$ 和 $a_2$ 都是适用的。通过以任意顺序取剩余步骤（保持每个 $\mathcal{S}_x (\overline E)$ 的内部顺序）来完成序列 $S$，使其成为 $merge(\overline S)$ 中的一个序列。（完）

---

> LEMMA 4.5: Let $\mathcal{AL}$ be a self-stabilizing algorithm for an arbitrary weak-exclusion task $WE$ in a system with a timeout mechanism. If $\mathcal{AL}$ has a circular execution $\overline E$, then $\mathcal{AL}$ has an infinite fair execution $E^\infty$, none of whose configurations is safe for $WE$.
>
> 引理 4.5：设 $\mathcal{AL}$ 为一个在具有超时机制的系统中针对任意弱排除任务 $WE$ 的自稳定算法。如果 $\mathcal{AL}$ 有一个循环执行 $\overline E$，那么 $\mathcal{AL}$ 有一个无限公平执行 $E^\infty$，其配置均不安全。

*Proof:*

Let $E$ be an arbitrary execution in $blowup(\overline E)$. Define $E^\infty$ as the infinite execution obtained by repeating $E$ forever. We show that no configuration in $E^\infty$ is safe.

Assume by way of contradiction that some configuration $c_1$ in $E^\infty$ is safe. Now we construct a finite circular execution $E'$ whose sequence of steps $S'$ is obtained by concatenating sequences from $merge(\overline S)$, that is $\mathcal{S}_x(E') = \mathcal{S}_x(\overline E)$. Since $\mathcal{AL}$ is an algorithm for some weak-exclusion task, $E'$ should have some set of steps $B = \{a_1, a_2\}$, where $a_i \in \mathcal{S}_i$, that are never applicable to a single configuration $c$ during $E'$. We reach a contradiction by refuting this statement for $E'$. To do this we choose some arbitrary enumeration $\mathcal{B} = B_1, ···, B_e$ of all the sets containing two steps, one of $P_s$ and the other of $P_r$. Execution $E'$ is constructed by first continuing the computation from $c_1$ as in $E$ until configuration $c_{init}$ is reached. Then we apply lemma 4.4 to extend $E'$ by $s$ consecutive executions $E_1, ···, E_e$, where $E_k$, $1 \leq k \leq e$, contains a configuration in which the steps in $B_k$ are applicable and that ends with $c_{init}$. The proof follows. (End)

*证明：*

设 $E$ 为 $blowup(\overline E)$ 中的任意执行。定义 $E^\infty$ 为通过无限重复 $E$ 获得的无限执行。我们证明 $E^\infty$ 中没有配置是安全的。

假设反证法，$E^\infty$ 中某个配置 $c_1$ 是安全的。现在我们构造一个有限循环执行 $E'$，其步骤序列 $S'$ 通过连接 $merge(\overline S)$ 中的序列获得，即 $\mathcal{S}_x(E') = \mathcal{S}_x(\overline E)$。由于 $\mathcal{AL}$ 是某个弱排除任务的算法，$E'$ 应该有一些步骤集合 $B = \{a_1, a_2\}$，其中 $a_i \in \mathcal{S}_i$，在 $E'$ 的单个配置 $c$ 中从未适用。我们通过反驳这个陈述来达到矛盾。为此，我们选择包含两个步骤的所有集合的任意枚举 $\mathcal{B} = B_1, ···, B_e$，其中一个来自 $P_s$，另一个来自 $P_r$。执行 $E'$ 通过首先从 $c_1$ 开始继续计算，直到达到配置 $c_{init}$。然后我们应用引理 4.4，通过 $s$ 个连续执行 $E_1, ···, E_e$ 扩展 $E'$，其中 $E_k$（$1 \leq k \leq e$）包含一个配置，在该配置中 $B_k$ 中的步骤是适用的，并以 $c_{init}$ 结束。证明完毕。（完）

---

The proof at the lower bound is completed by the following theorem.

下界证明由以下定理完成。

---

> THEOREM 4.2: Let $\mathcal{AL}$ be a self-stabilizing algorithm for an arbitrary weak-exclusion task in a system with a timeout mechanism. For every execution $E$ of $\mathcal{AL}$, all the configurations of $E$ are distinct. Hence, for every $t > 0$, the size of at least one of the first $t$ configurations in $E$ is at least $\lceil \log2(t) \rceil$.
>
> 定理 4.2：设 $\mathcal{AL}$ 为一个在具有超时机制的系统中针对任意弱排除任务的自稳定算法。对于 $\mathcal{AL}$ 的每一个执行 $E$，$E$ 的所有配置都是不同的。因此，对于每一个 $t > 0$，$E$ 的前 $t$ 个配置中至少有一个的大小至少为 $\lceil \log2(t) \rceil$。

*Proof:*

Assume by way of contradiction that there exists an execution $E$ of $\mathcal{AL}$ in which not all the configurations are distinct; then $E$ contains a circular sub-execution $\overline E$. By lemma 4.5, there exists an infinite execution $E'$ of $\mathcal{AL}$ that is obtained by an infinite repetition of executions from $blowup(\overline E)$ and that never reaches a safe configuration— a contradiction. (End)

*证明：*

假设反证法，存在一个 $\mathcal{AL}$ 的执行 $E$，其中并非所有配置都是不同的；那么 $E$ 包含一个循环子执行 $\overline E$。根据引理 4.5，存在一个 $\mathcal{AL}$ 的无限执行 $E'$，通过无限重复 $blowup(\overline E)$ 中的执行获得，并且永远不会达到安全配置——这与假设矛盾。（完）

---

The implication of the above result is that no token-passing or stop-and-wait data-link algorithm exists with bounded memory resources. The memory of at least one of the system components must grow, either the memory of a processor or the number of bits in a message queue. In the next section we propose ways to cope in practice with the implications of the lower bound.

上述结果的含义是不存在具有有限内存资源的令牌传递或停等数据链路算法。系统组件中的至少一个的内存必须增长，要么是处理器的内存，要么是消息队列中的位数。在下一节中，我们提出了应对下界影响的实际方法。

## Bounded-Link and Randomized Solutions

It is customarily assumed that a sixty-four-bit counter can implement an unbounded counter for every possible application. The time it takes to reach the largest possible value of this sixty-four-bit counter is enormous. However, in the context of self-stabilizing algorithms, a single transient fault may cause the counter to reach its upper limit instantly.

通常假设，一个六十四位计数器可以实现一个无界计数器，适用于所有可能的应用。达到这个六十四位计数器的最大可能值所需的时间是巨大的。然而，在自稳定算法的背景下，单个瞬态故障可能会导致计数器立即达到其上限。

In light of the lower bound above, it is impossible to devise a self-stabilizing bounded solution for the token-passing task in a completely asynchronous system, where the number of messages in transit is not bounded. In this section we relax the assumptions on asynchronous systems to obtain a bounded self-stabilizing algorithm for the token-passing task $TP$. For our first algorithm, we assume that a bound on the number of messages in transit is known.

鉴于上述下界的说明，在完全异步系统中设计一个自稳定的有界令牌传递任务解决方案是不可能的，其中在途消息的数量是无界的。在本节中，我们放宽了对异步系统的假设，以获得一个有界的自稳定令牌传递任务 $TP$ 的算法。对于我们的第一个算法，我们假设已知在途消息的数量上限。

**Let $cap$ be the bound on the total number of messages in both directions. Interestingly, a bounded version of the algorithm presented in figure 4.1, in which the counter of the sender is incremented modulo $cap+1$, is self-stabilizing. Roughly speaking, the reason is that the sender must eventually introduce a counter value not existing in any message in transit.**

**设 $cap$ 为双向消息总数的上限。有趣的是，图 4.1 中所示算法的有界版本，其中发送方的计数器以 $cap+1$ 为模递增，是自稳定的。大致来说，原因是发送方最终必须引入一个在途消息中不存在的计数器值。**

![figure_4.2](images/figure_4.2.png)

**The second self-stabilizing bounded algorithm that we propose here does not assume any bound on the number of messages in transit**. In this algorithm, **the sender tosses a coin to decide on the label of messages, and hence the algorithm is randomized**. A randomized algorithm requires an additional (hardware) device with random output. Moreover, the stabilization to a safe configuration is with *probability* 1. Thus, there can exist executions $E$ of any finite length such that no safe configuration is reached in $E$. The code of the algorithm appears in figure 4.2. **At least three labels are used, {0, 1, 2}**. **The sender repeatedly sends a message with a particular label $l$ until a message with the same label $l$ arrives. Then the sender chooses randomly (by invoking the procedure *ChooseLabel*) the next label $l' \neq l$ from the remaining labels**. The program of the receiver is the same as in the unbounded algorithm.

我们在此提出的**第二个自稳定有界算法不假设在途消息的数量有任何限制**。在这个算法中，**发送方通过抛硬币来决定消息的标签，因此该算法是随机化的**。随机化算法需要一个具有随机输出的额外（硬件）设备。此外，稳定到安全配置的概率为 1。因此，可能存在任意有限长度的执行 $E$，在 $E$ 中没有达到安全配置。算法的代码见图 4.2。**至少使用了三个标签，{0, 1, 2}**。**发送方重复发送具有特定标签 $l$ 的消息，直到收到具有相同标签 $l$ 的消息。然后，发送方从剩余标签中随机选择（通过调用 *ChooseLabel* 过程）下一个标签 $l' \neq l$**。接收方的程序与无界算法中的相同。

Now we present the main ideas for proving the self-stabilization property of the algorithm in figure 4.2 (similar ideas were presented in section 2.10 for proving that the alternating-bit algorithm is pseudo self-stabilizing). In configuration $c$, let $\mathcal{L}_{sr}(c) = l_1, l_2, l_3, ···, l_k$ be the sequence of the labels of the messages that are pending from the sender to the receiver, and let $\mathcal{L}_{rs}(c) = l_{k+1}, l_{k+2}, l_{k+3}, ···, l_{k+q}$ be the sequence of the labels of the messages that are pending from the receiver to the sender. The label $l_1 (l_{k+1})$ is the label of the last message sent by the sender (receiver, respectively) and the label $l_k (l_{k+q})$ is the label of the message that is the first to arrive at the receiver (sender, respectively). Let $\mathcal{L}(c) = l_1, l_2, ···, l_k, l_{k+1}, l_{k+2}, ···, l_{k+q}$ be the concatenation of $\mathcal{L}_{sr}(c)$ with $\mathcal{L}_{rs}(c)$.

现在我们介绍证明图 4.2 中算法自稳定性性质的主要思想（类似的思想在第 2.10 节中用于证明交替位算法是伪自稳定的）。在配置 $c$ 中，设 $\mathcal{L}_{sr}(c) = l_1, l_2, l_3, ···, l_k$ 为从发送方到接收方的待处理消息的标签序列，$\mathcal{L}_{rs}(c) = l_{k+1}, l_{k+2}, l_{k+3}, ···, l_{k+q}$ 为从接收方到发送方的待处理消息的标签序列。标签 $l_1 (l_{k+1})$ 是发送方（接收方）发送的最后一条消息的标签，标签 $l_k (l_{k+q})$ 是接收方（发送方）首先收到的消息的标签。设 $\mathcal{L}(c) = l_1, l_2, ···, l_k, l_{k+1}, l_{k+2}, ···, l_{k+q}$ 为 $\mathcal{L}_{sr}(c)$ 和 $\mathcal{L}_{rs}(c)$ 的连接。

A segment of labels $\mathcal{S}(c) = l_j, l_{j+1}, ···, l_{j+n}$ in $\mathcal{L}(c)$ is a maximal sequence of labels in $\mathcal{L}(c)$ such that all the labels in $\mathcal{S}(c)$ are identical. Let $SegmentsNumber(\mathcal{L}(c))$ be the number of segments in $\mathcal{L}(c)$.

标签段 $\mathcal{S}(c) = l_j, l_{j+1}, ···, l_{j+n}$ 是 $\mathcal{L}(c)$ 中的一个最大标签序列，使得 $\mathcal{S}(c)$ 中的所有标签都是相同的。设 $SegmentsNumber(\mathcal{L}(c))$ 为 $\mathcal{L}(c)$ 中的段数。

We use the term *pseudo-stabilized configuration* for every configuration $c$ in which the value of $MsgLabel$ of the next (pending) message $m_{k+q}$ to arrive to the sender is equal to the label variable of the sender in $c$. Note that a pseudo-stabilized configuration $c$ is a safe configuration when $SegmentsNumber(\mathcal{L}(c)) = 1$. Let $c_1$ and $c_2$ be two successive pseudo-stabilized configurations in an arbitrary execution. We claim that $SegmentsNumber(\mathcal{L}(c_1))$ $≥$ $SegmentsNumber(\mathcal{L}(c_2))$. Moreover, if the number of segments in $c_1$ is greater than one, then, with probability 1/2, the number of segments in $c_2$ is less than the number of segments in $c_1$.

我们使用术语 *伪稳定配置* 来表示每个配置 $c$，其中到达发送方的下一个（待处理）消息 $m_{k+q}$ 的 $MsgLabel$ 值等于 $c$ 中发送方的标签变量。注意，当 $SegmentsNumber(\mathcal{L}(c)) = 1$ 时，伪稳定配置 $c$ 是一个安全配置。设 $c_1$ 和 $c_2$ 为任意执行中的两个连续伪稳定配置。我们声称 $SegmentsNumber(\mathcal{L}(c_1))$ $≥$ $SegmentsNumber(\mathcal{L}(c_2))$。此外，如果 $c_1$ 中的段数大于 1，那么以 1/2 的概率，$c_2$ 中的段数小于 $c_1$ 中的段数。。

Starting in $c_1$, when the sender receives $m_{k+q}$, the sender chooses a new label — say 2 — that is not equal to the label of $m_{k+q}$, say $l_{k+q} = 0$. It is easy to see that the segment $\mathcal{S}$ to which $m_{k+q}$ belonged is eliminated before the next pseudo-stabilized configuration is reached. At the same time, a new segment with label 2 is introduced as the prefix of $\mathcal{L}$. Moreover, if the label of the segment $\mathcal{S'}$ that follows $\mathcal{S}$ is 1, $\mathcal{S'}$ is eliminated as well. In this latter case the number of segments is reduced. The schedule-luck game technique can be used to prove that the system reaches a safe configuration. Luck always chooses a label that is different from the label of the segment $\mathcal{S'}$. The combined probability of luck's strategy is no more than $2^{−(2·sn)}$, where $sn = SegmentsNumber(\mathcal{L}(c))$. Luck's strategy ensures that the number of segments is reduced by a factor of two in an execution that starts in $c$ and ends in $c'$, where $c'$ immediately follows a step in which the sender receives an acknowledgment for the first message it sent during the execution. The same holds for the execution that starts in $c'$. Thus, the number of choices until a safe configuration is reached is $sn + sn/2 + sn/4 + ··· = 2 · sn$. The number of rounds in the game is $O((k+q) · \log(k+q))$, where $k+q$ is the number of messages in the first configuration $c$. Thus, a safe configuration is reached within at most $O((k+q) · \log(k+q) · 2^{2·sn})$ expected number of rounds. We note that the expected number of rounds until a safe configuration is reached can be reduced when the number of labels used is greater than 3.

从 $c_1$ 开始，当发送方收到 $m_{k+q}$ 时，发送方选择一个新的标签——比如 2——该标签不等于 $m_{k+q}$ 的标签，比如 $l_{k+q} = 0$。很容易看出，$m_{k+q}$ 所属的段 $\mathcal{S}$ 在达到下一个伪稳定配置之前被消除。同时，一个新的标签为 2 的段作为 $\mathcal{L}$ 的前缀被引入。此外，如果紧随 $\mathcal{S}$ 的段 $\mathcal{S'}$ 的标签为 1，$\mathcal{S'}$ 也会被消除。在后一种情况下，段的数量减少。可以使用调度运气游戏技术来证明系统达到了安全配置。运气总是选择一个与段 $\mathcal{S'}$ 的标签不同的标签。运气策略的综合概率不超过 $2^{−(2·sn)}$，其中 $sn = SegmentsNumber(\mathcal{L}(c))$。运气策略确保在从 $c$ 开始并在 $c'$ 结束的执行中，段的数量减少一半，其中 $c'$ 紧随发送方收到其在执行期间发送的第一条消息的确认步骤之后。同样的情况适用于从 $c'$ 开始的执行。因此，直到达到安全配置的选择次数为 $sn + sn/2 + sn/4 + ··· = 2 · sn$。游戏中的轮数为 $O((k+q) · \log(k+q))$，其中 $k+q$ 是第一个配置 $c$ 中的消息数量。因此，在最多 $O((k+q) · \log(k+q) · 2^{2·sn})$ 预期轮数内达到安全配置。我们注意到，当使用的标签数量大于 3 时，达到安全配置的预期轮数可以减少。

## Self-Stabilizing Simulation of Shared Memory

In this section we present a method for simulating self-stabilizing, shared memory algorithms by self-stabilizing, message passing algorithms. The simulated algorithms are assumed to be in the link-register model in which communication between neighbors $P_i$ and $P_j$ is carried out using a two-way link. The link is implemented by two shared registers that support read and write atomic operations. Processor $P_i$ reads from one register and writes in the other, while these functions are reversed for $P_j$. In the system that simulates the shared memory, every link is simulated by two directed links: one from $P_i$ to $P_j$ and the other from $P_j$ to $P_i$. **The heart of the simulation is a self-stabilizing implementation of the read and write operations**.

在本节中，我们提出了一种通过自稳定消息传递算法模拟自稳定共享内存算法的方法。假设模拟的算法在链接寄存器模型中，其中邻居 $P_i$ 和 $P_j$ 之间的通信是通过双向链接进行的。该链接由两个支持读写原子操作的共享寄存器实现。处理器 $P_i$ 从一个寄存器读取并向另一个寄存器写入，而这些功能对于 $P_j$ 是相反的。在模拟共享内存的系统中，每个链接由两个定向链接模拟：一个从 $P_i$ 到 $P_j$，另一个从 $P_j$ 到 $P_i$。**模拟的核心是读写操作的自稳定实现**。

**The proposed simulation implements these operations by using a self-stabilizing, token-passing algorithm**. The token-passing algorithm may be chosen from the algorithms described in the previous section. For any pair of neighbors, we run the algorithm on the two links connecting them. In order to implement our self-stabilizing, token-passing algorithm, we need to define, for each link, which of the processors acts as the sender and which acts as the receiver.

**所提出的模拟通过使用自稳定令牌传递算法来实现这些操作**。令牌传递算法可以从前一节描述的算法中选择。对于任何一对邻居，我们在连接它们的两个链接上运行该算法。为了实现我们的自稳定令牌传递算法，我们需要为每个链接定义哪个处理器作为发送方，哪个作为接收方。

**We assume that the processors have distinct identifiers**. Every message sent by each of the processors carries the identifier of that processor. Eventually each processor knows the identifier of all its neighbors. **In each link, the processor with the larger identifier acts as the sender while the other processor acts as the receiver**. Since each pair of neighbors uses a different instance of the algorithm, a separate timeout mechanism is needed for each such pair.

**我们假设处理器具有不同的标识符**。每个处理器发送的每条消息都带有该处理器的标识符。最终，每个处理器都知道其所有邻居的标识符。**在每个链接中，标识符较大的处理器作为发送方，而另一个处理器作为接收方**。由于每对邻居使用算法的不同实例，因此每对邻居需要一个单独的超时机制。

We now describe the simulation of some arbitrary link $\mathscr{e}$ connecting $P_i$ and $P_j$. In the shared memory model, $\mathscr{e}$ is implemented by a register $r_{ij}$ into which $P_i$ writes and from which $P_j$ reads, and by a register $r_{ji}$ for which the roles are reversed. In the simulating algorithm, processor $P_i$ ($P_j$) has a local variable called $R_{ij}$ ($R_{ji}$) that holds the values of $r_{ij}$ ($r_{ji}$, respectively). Every token has an additional field called $value$. Every time $P_i$ receives a token from $P_j$, $P_i$ writes the current value of $R_{ij}$ in the $value$ field of that token. A write operation of $P_i$ into $r_{ij}$ is simply implemented by locally writing into $R_{ij}$. A read operation of $P_i$ from $r_{ji}$ is implemented by the following steps:

1. $P_i$ receives the token from $P_j$, and then
2. $P_i$ receives the token again from $P_j$. The value the read operation returns is the value attached to the token (when it arrives at this second time).

现在我们描述连接 $P_i$ 和 $P_j$ 的任意链接 $\mathscr{e}$ 的模拟。在共享内存模型中，$\mathscr{e}$ 由一个寄存器 $r_{ij}$ 实现，$P_i$ 向其中写入，$P_j$ 从中读取，另一个寄存器 $r_{ji}$ 的角色相反。在模拟算法中，处理器 $P_i$ ($P_j$) 有一个名为 $R_{ij}$ ($R_{ji}$) 的局部变量，分别保存 $r_{ij}$ ($r_{ji}$) 的值。每个令牌有一个名为 $value$ 的附加字段。每次 $P_i$ 从 $P_j$ 接收到令牌时，$P_i$ 将 $R_{ij}$ 的当前值写入该令牌的 $value$ 字段。$P_i$ 向 $r_{ij}$ 的写操作仅通过本地写入 $R_{ij}$ 实现。**$P_i$ 从 $r_{ji}$ 的读操作通过以下步骤实现**：

1. **$P_i$ 从 $P_j$ 接收令牌，然后**
2. **$P_i$ 再次从 $P_j$ 接收令牌。读操作返回的值是附加到令牌上的值（当它第二次到达时）**。

Note that, during the simulation of a read operation from $r_{ji}$, $P_i$ does not proceed in its program until the simulated read step is finished. However, $P_i$ continues to send messages (and tokens) to each of its neighbors that serve any read requests.

注意，在模拟从 $r_{ji}$ 的读操作期间，$P_i$ 不会在其程序中继续进行，直到模拟的读步骤完成。然而，$P_i$ 继续向其每个邻居发送消息（和令牌），以处理任何读请求。

The correctness of the simulation is proved by showing that every execution $E$ has a suffix in which it is possible to $linearize$ all the simulated read and write operations executed in $E$. Operations in an execution can be linearized if there is a total order of all the simulated operations in which the execution order of the simulated operations of every individual processor is preserved and the result of every read operation from a register $r$ is the last value (according to the total order) written in $r$, if such a write exists, or a constant value $x$ otherwise. We consider $x$ to be the value of $r$ prior to the first write operation.

通过证明每个执行 $E$ 都有一个后缀，在该后缀中可以将所有模拟的读写操作线性化，从而证明模拟的正确性。如果存在所有模拟操作的全序，其中每个处理器的模拟操作的执行顺序得以保留，并且从寄存器 $r$ 进行的每次读操作的结果是最后写入 $r$ 的值（根据全序），如果存在这样的写操作，否则为常数值 $x$，则执行中的操作可以线性化。我们认为 $x$ 是第一次写操作之前 $r$ 的值。

Any execution of a simulated read operation by a processor $P_i$ starts in some particular configuration $c_r$ and ends in a later configuration $c_{r+k}$ for some $k$. $P_i$ does not proceed in executing steps of the simulated program until $c_{r+k}$ is reached; in particular, $P_i$ does not change the value of any $R_{i,l}$ between $c_r$ and $c_{r+k}$. Therefore, without influencing the operations of the other processors, we can choose any index between $r$ and $r+k$ as the index of the step in which the simulated read operation of $P_i$ occurs. Our simulation method, and in particular the requirement to wait for the second token, guarantees that the result obtained by the simulated read operation is a value of $R_{ji}$ between $c_r$ and $c_{r+k}$.

处理器 $P_i$ 执行的任何模拟读操作从某个特定配置 $c_r$ 开始，并在某个 $k$ 的后续配置 $c_{r+k}$ 结束。在达到 $c_{r+k}$ 之前，$P_i$ 不会继续执行模拟程序的步骤；特别是，$P_i$ 不会在 $c_r$ 和 $c_{r+k}$ 之间更改任何 $R_{i,l}$ 的值。因此，在不影响其他处理器操作的情况下，我们可以选择 $r$ 和 $r+k$ 之间的任何索引作为 $P_i$ 进行模拟读操作的步骤索引。我们的模拟方法，特别是等待第二个令牌的要求，保证了通过模拟读操作获得的结果是 $c_r$ 和 $c_{r+k}$ 之间的 $R_{ji}$ 的值。

The above observations are used to map a time index for each simulated read and write operation. Define the time index of a simulated write operation to $r_{ij}$ to be the index of the step in $E$ in which the corresponding local write operation to $R_{ij}$ is executed. Define the time index of a simulated read operation of $P_j$ from $r_{ij}$ to be the index of the step in $E$ in which $P_i$ sends the value of its local variable $R_{ij}$ attached to the token that later reaches $P_j$ in step (2) of the simulated read.

上述观察用于映射每个模拟读写操作的时间索引。将模拟写操作到 $r_{ij}$ 的时间索引定义为 $E$ 中执行相应本地写操作到 $R_{ij}$ 的步骤索引。将 $P_j$ 从 $r_{ij}$ 进行的模拟读操作的时间索引定义为 $E$ 中 $P_i$ 发送其本地变量 $R_{ij}$ 的值并附加到令牌上的步骤索引，该令牌稍后在模拟读的步骤（2）中到达 $P_j$。

Once each link holds a single token, all the operations to a register $R_{ij}$ are linearized to be executed in the step with the mapped index, where each read operation from $R_{ij}$ returns the last value written to $R_{ij}$.

一旦每个链接持有一个令牌，所有对寄存器 $R_{ij}$ 的操作都被线性化为在映射索引的步骤中执行，其中每次从 $R_{ij}$ 进行的读操作返回最后写入 $R_{ij}$ 的值。
