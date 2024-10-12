# note_4_3_更新算法

包括 4.4 以及 PPT 内容。很不幸的是，这一部分没有任何部分被省略。此外，书中这一节的逻辑不是一般的乱，不结合 PPT 基本看不懂。

## 介绍算法前的梳理

### 更新算法的任务

> **The task of the most basic form of the update algorithm is to inform each processor of the identifiers of the processors that are in its *connected component*. Processors that can communicate directly or indirectly are in the same connected component**. Therefore, every processor knows the maximal identifier in the system and a single leader is in fact elected.
>
> **更新算法最基本形式的任务是通知每个处理器其 *连接组件* 中的处理器标识符。能够直接或间接通信的处理器在同一个连接组件中。**因此，每个处理器都知道系统中的最大标识符，并且实际上选出了一个领导者。

### 更新算法的一个实质

> A question to ask at this stage is **whether there is a leader-election algorithm that stabilizes within $O(d)$ cycles, where $d$ is the actual diameter of the connected component. The self-stabilizing update algorithm that is presented here gives a positive answer to the above question**.
>
> 在这个阶段要问的问题是，**是否存在一个在 $O(d)$ 周期内稳定的领导者选举算法，其中 $d$ 是连接组件的实际直径。这里提出的自稳定更新算法对上述问题给出了肯定的回答**。

或者换一个说法：The update algorithm can be viewed as a self-stabilizing leader election algorithm within $O(d)$ cycles (pulses).

### 每个处理器都有一个唯一的标识符

为什么更新算法的前提是：**每个处理器都有一个唯一的标识符**？很诡异的是，书中并没有直接说明，而是通过 2.6 中的自稳定互斥算法一定需要特殊处理器的例子来间接说明的。

> **Dijkstra proved that, without this special processor, it is impossible to achieve mutual exclusion in a self-stabilizing manner**.
>
> **Dijkstra 证明了，如果没有这个特殊处理器，就不可能以自稳定的方式实现互斥**。

为此提出了一种被称为 proof of the impossibility result 的技术，用于专门说明不可能。它给出了两种情况：

- 连接成环，并由中央守护进程激活的，复合（非素数）数量的处理器
- 连接成环，不存在中央处理器，素数数量的处理器

这两种情况的证明都是通过对称达成的。

---

直观的思考为何必须要有唯一标识符：

自稳定算法一定要某些方式去打破对称性，有三种方法：

1. 唯一标识符（unique identifier），或者称为 ID-based
2. 拥有特殊处理器（special/distinguish processor）
3. 随机性（random, e.g. sl-game）

对于 leader-election 问题，目的是 2，那么手段就是 1 或者 3；

- 如果一开始就有 ID，那么就是之前学过的 Leader Election in a General Communication Network (ID-based)；
- 如果没有 ID，比如完全图或者高度对称，那么就要靠随机性，那么就是之前学过的 Leader Election in Complete Graph (random)。

### 标题理解

Update: Converting a Special Processor to an Id-based Dynamic System

目标：将一个特殊处理器的系统转化为有独特 ID 的动态系统。

（隐含：原本的算法适用于特殊处理器系统）

更新算法：也是一种领导者选举算法，需要有唯一标识符。

过程：首先使用更新算法得到领导者，也就是特殊处理器；然后再使用原本的算法；那么整体上看，这整个过程就是将适用于特殊处理器系统的原本的算法改造成了适用于有独特 ID 的动态系统的算法，目标达成。

## 更新算法
