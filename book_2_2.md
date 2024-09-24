# 2.2 Self-Stabilization Requirements

**A self-stabilizing system can be started in any arbitrary configuration and will eventually exhibit a desired “legal” behavior.**

**We define the desired legal behavior by a set of legal executions denoted $LE$.** A set of legal executions is defined for a particular system and a particular task. **Every system execution of a self-stabilizing system should have a suffix that appears in $LE$.** For instance, when the task is *mutual exclusion*, the task is defined by the set of legal executions in which, in every configuration, there is at most one processor in the critical section, and in which every processor is in the critical section in an infinite number of configurations of the execution.

**A configuration $c$ is *safe* with regard to a task $LE$ and an algorithm if every fair execution of the algorithm that starts from $c$ belongs to $LE$.**

**An algorithm is *self-stabilizing* for a task $LE$ if every fair execution of the algorithm reaches a safe configuration with relation to $LE$.**

**自稳定系统可以从任何任意配置开始，并最终表现出期望的“合法”行为。**

**我们通过一组合法执行（记为 $LE$）来定义期望的合法行为。**对于特定系统和特定任务，定义了一组合法执行。**自稳定系统的每个系统执行都应该有一个后缀出现在 $LE$ 中。**例如，当任务是 *互斥* 时，该任务由一组合法执行定义，在每个配置中，最多只有一个处理器在临界区，并且每个处理器在执行的无限多个配置中都在临界区。

**对于任务 $LE$ 和算法，如果从配置 $c$ 开始的算法的每个公平执行都属于 $LE$，则配置 $c$ 对该任务是安全的。**

**如果算法的每个公平执行都达到与任务 $LE$ 相关的安全配置，则该算法对任务 $LE$ 是 *自稳定* 的。**
