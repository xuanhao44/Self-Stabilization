# note_2_7_完全图中的领导者选举

包含 2.9 章部分内容和 PPT 内容。

## Randomized Self-Stabilization

Assumptions and Definitions

- Processor activity is managed by a scheduler
- The scheduler’s assumption
  - at most one atomic step is executed in every given time
  - Each atomic step includes a single read or write operation and a finite sequence of local computation.
- The scheduler is regarded as an adversary
  - It aims to increase the algorithm costs as much as possible
- The scheduler is assumed to be fair, yet it has unlimited resources and chooses the next activated processor in an on-line manner.

An algorithm is randomized self-stabilizing for task $LE$ if, starting with any system configuration and considering any fair scheduler, the algorithm reaches a safe configuration within a finite number of rounds.

**Randomized algorithms are often used to break symmetry in a system of totally identical processors.**

## Coarse（粗粒度原子性）vs. Fine Atomicity

Under coarse atomicity---less power to the adversary

- a coin toss is an internal operation that is not separable from the next read or write operation.
- the adversary is unaware to the algorithm random choices before its first read or write operation

Under fine atomicity---more power to the adversary

- a coin toss is a separate operation from the next read or write operation.
- the adversary is aware to the algorithm random choices before its first read or write operation

## Scheduler-Luck Game（略，纯粹的概念）

## Self-Stabilizing Leader Election in Complete Graphs

### 一些术语

#### 关于 Central daemon 和 Distributed Demon

Under the central demon model: **exactly one among all processors** is arbitrarily selected by an adversarial scheduler to take a single atomic step.

Under the distributed demon model: **an arbitrary number of processors** are selected by an adversarial scheduler to concurrently take atomic steps.

书中没有提到 distributed demon，但是实际上就是指 central daemon 不存在的情况。在这种情况下，其实很容易出现所有人同时站起来，然后所有人同时坐下，这种行为可以永远重复。这挺像（操作系统）哲学家就餐中的活锁问题。

> Thus, there is no self-stabilizing uniform leader election algorithm without a central daemon.
>
> 因此，没有中央守护进程就没有自稳定的统一领导者选举算法。

Unfortunately, there is no deterministic solution under distributed demon.

我们不会研究 distributed demon，因为在领导者选举算法的前提下，没有与之对应的确定性的解。

### 关于假设前提

> Our simple randomized self-stabilizing leader-election algorithm does not assume the existence of a central daemon. The settings presented in section 2.1 of this chapter are assumed: each step consists of a single read or a single write operation.
>
> 我们的简单随机自稳定领导者选举算法不假设中央守护进程的存在。假设本章第 2.1 节中提出的设置：每一步由单个读取或单个写入操作组成。

前面提到，没有中央守护进程就没有自稳定的统一领导者选举算法。

但实际上我们可能并不是一定要求中央守护进程，而是它的一个重要性质（必要条件），也就是每一步由单个读取或单个写入操作组成。按照 2.1 的说法，我们是假设并发是不会影响的。

实际上我们在作业中看到了很奇特的说法：

> Consider an asynchronous computer network with a distributed but fair scheduler, i.e., not a central daemon.

这说明 distributed but fair scheduler 也应该能满足要求。
