# 2 Definitions, Techniques, and Paradigms

This chapter is devoted to formalizing the distributed system, the basic assumptions, the requirements, and the complexity measures. The formal definitions are then used in the description of basic techniques and paradigms in the design of self-stabilizing algorithms and in proving their correctness.

本章致力于形式化分布式系统、基本假设、需求和复杂性度量。然后，这些形式化定义将用于描述自稳定算法设计中的基本技术和范式，并证明其正确性。

- [2.1 Definitions of the Computational Model](book_2_1.md)
  - [2.2 Self-Stabilization Requirements](book_2_2.md)
  - [2.3 Complexity Measures](book_2_3.md)
  - [2.4 Randomized Self-Stabilization](book_2_4.md)
  - [2.5 Example: Spanning-Tree Construction](book_2_5.md)
  - [2.6 Example: Mutual Exclusion](book_2_6.md)
  - [2.7 Fair Composition of Self-Stabilizing Algorithms](book_2_7.md)
    - [Example: Mutual Exclusion for General Communication Graphs](book_2_7.md#example-mutual-exclusion-for-general-communication-graphs)
  - [2.9 Proof Techniques](book_2_9.md)
    - [Variant Function](book_2_9.md#variant-function)
    - [Example: Self-Stabilizing Maximal Matching](book_2_9.md#example-self-stabilizing-maximal-matching)
    - [Convergence Stairs](book_2_9.md#convergence-stairs)
    - [Example: Leader Election in a General Communication Network](book_2_9.md#example-leader-election-in-a-general-communication-network)
    - [Scheduler-Luck Game](book_2_9.md#scheduler-luck-game)
    - [Example: Self-Stabilizing Leader Election in Complete Graphs](book_2_9.md#example-self-stabilizing-leader-election-in-complete-graphs)
    - [Neighborhood Resemblance](book_2_9.md#neighborhood-resemblance)
    - [Example: Spanning-Tree Construction](book_2_9.md#example-spanning-tree-construction)
