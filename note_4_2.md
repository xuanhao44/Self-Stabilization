# note_4_2_排名算法

包括 4.3 以及 PPT 内容。

## 理解在干什么

首先标题是 Converting an Id-based System to a Special-processor System，但是排名任务（算法）却是为系统中的 $n$ 个处理器分配一个范围为 1 到 $n$ 的唯一标识符。这可能会让人有些疑惑。

但是注意，我们这一节要讨论的是 compiler：

> Our goal is to design a compiler that converts self-stabilizing algorithm for a unique *ID* system to work in special processor system.

所以原本的算法，我们称之为 $\mathcal{AL}$，它是适用于唯一标识符系统的。与我们这一节的排名算法结合，具体为：

对于一个有特殊处理器的系统，我们首先使用排名算法，这样我们就得到了唯一标识符；接着我们再使用原本的算法 $\mathcal{AL}$，故而故而就成功的将 $\mathcal{AL}$ 算法的唯一标识符系统的前提转换成了特殊处理器系统。

## 排名算法构成

We will form the self-stabilizing *ranking* algorithm by running 3 self-stabilizing algorithms one after the other:

1. Self-Stabilizing spanning tree construction (Sec. 2.5)
2. Self-Stabilizing counting algorithm
3. Self-Stabilizing naming algorithm

生成树没啥好说的，它构成的生成树是 counting 算法的前提。这一节的重点是 counting 和 naming 算法。

计数算法是由叶子结点出发，然后根据高度归纳推理证明其正确性的。而对于命名算法，首先要清楚它是在计数算法的基础上进行的，其次他是从根出发，然后根据高度归纳推理证明其正确性的。证明过程刚好相反。

counting 和 naming 算法的证明过程书中并没有给出，但是 PPT 中有，可以去看。

## 书中错误的无根树计数算法

书中提到了无根树计数算法，思想是很好的，但是很遗憾的是算法写的是有问题的。对于这个问题，在作业中有体现，这里就不多说了。
