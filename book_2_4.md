# 2.4 Randomized Self-Stabilization

随机自稳定

So far we have not concerned ourselves with randomized algorithms — i.e., those that use coin-toss or random-function results to determine their actions. An important subject in self-stabilization research is the study of randomized self-stabilizing algorithms. Breaking symmetry is sometimes impossible without using randomization.

In order to define the requirements for randomized self-stabilizing algorithms, we use the following assumptions and definitions. Processor activity is managed by a scheduler. The scheduler is merely an abstraction of the assumption made for the interleaving model, that at most one step is executed in every given time. In any given configuration, the scheduler activates a single processor, which executes a single step. To ensure correctness of the algorithms, we regard the scheduler as an adversary. The scheduler is assumed to have unlimited resources and chooses the next activated processor *on line*, using all the information about the execution so far. A scheduler $S$ is *fair* if, for any configuration $c$ with probability 1, an execution starting from $c$ in which processors are activated by $S$ is fair.

Finally, an algorithm is *randomized self-stabilizing* for a task $LE$ if, starting with any system configuration and considering any fair scheduler, the algorithm reaches a safe configuration within an expected number of rounds that is bounded by some constant $k$ ($k$ may depend on $n$, the number of processors in the system).

Randomized algorithms are often used to break symmetry in a system of totally identical processors in which processors do not have unique identifiers. The terms *uniform* or *anonymous system* are used for such systems.

到目前为止，我们还没有涉及随机算法——即使用抛硬币或随机函数结果来决定其动作的算法。自稳定研究中的一个重要课题是随机自稳定算法的研究。在某些情况下，不使用随机化是无法打破对称性的。

为了定义随机自稳定算法的要求，我们使用以下假设和定义。处理器活动由调度器管理。调度器只是交错模型假设的抽象，即在任何给定时间内最多执行一个步骤。在任何给定配置中，调度器激活一个处理器，该处理器执行一个步骤。为了确保算法的正确性，我们将调度器视为对手。假设调度器拥有无限资源，并 *在线* 选择下一个激活的处理器，使用到目前为止的所有执行信息。如果对于任何配置 $c$，以概率 1 从 $c$ 开始的执行中，调度器 $S$ 激活处理器是公平的，则称调度器 $S$ 是 *公平* 的。

最后，如果对于任务 $LE$，从任何系统配置开始并考虑任何公平调度器，算法在期望的轮数内达到安全配置，并且该期望轮数由某个常数 $k$（$k$ 可能依赖于系统中的处理器数量 $n$）界定，则该算法是 *随机自稳定* 的。

随机算法通常用于在完全相同的处理器系统中打破对称性，这些处理器没有唯一标识符。对于这样的系统，使用 *统一* 或 *匿名系统* 术语。
