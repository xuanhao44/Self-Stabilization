# Self-Stabilization

用于 chalmers 的 EDA387 Computer Networks 课。是不完全的书本翻译和笔记。

- 书本
  - [Self-Stabilization (Shlomi Dolev)](resources/Self-Stabilization-Shlomi-Dolev.pdf)
- PPT
  - [Algorithms2024-01Dijkstra](resources/Algorithms2024-01Dijkstra.pptx)
  - [Algorithms2024-01Models](resources/Algorithms2024-01Models.pptx)
  - [Algorithms2024-02Spanning](resources/Algorithms2024-02Spanning.pptx)
  - [Algorithms2024-03Matching](resources/Algorithms2024-03Matching.pptx)
  - [Algorithms2024-04LeaderElection](resources/Algorithms2024-04LeaderElection.pptx)
  - [Algorithms2024-05DataLink](resources/Algorithms2024-05DataLink.pptx)
  - [Algorithms2024-07ModelConversions](resources/Algorithms2024-07ModelConversions.pptx)
  - [Algorithms2024-08PresenceFaults](resources/Algorithms2024-08PresenceFaults.pptx)
  - [Algorithms2024-09SuperStabilization](resources/Algorithms2024-09SuperStabilization.pptx)
- 作业
  - [algorithmLab2024](resources/algorithmLab2024.pdf)

---

由于 GitHub 的 $\LaTeX$ 渲染的不完全性，建议使用 Typora 或者 VS Code 打开看。

- 正文
  - [1 Introduction](book_1.md)
  - [2 Definitions, Techniques, and Paradigms](book_2.md)
  - [3 Motivating Self-Stabilization](book_3.md)
  - [4 Self-Stabilizing Algorithms for Model Conversions](book_4.md)
  - [6 Convergence in the Presence of Faults](book_6.md)
  - [7 Local Stabilization](book_7.md)
- 笔记
  - [2_1 概念](note_2_1.md)
    - [2.1 Definitions of the Computational Model](book_2_1.md)
    - [2.2 Self-Stabilization Requirements](book_2_2.md)
    - [2.3 Complexity Measures](book_2_3.md)
  - [2_2 互斥自稳定算法](note_2_2.md)
    - [2.6 Example: Mutual Exclusion](book_2_6.md)
  - [2_3 生成树构造](note_2_3.md)
    - [2.5 Example: Spanning-Tree Construction](book_2_5.md)
  - [2_4 公平合成](note_2_4.md)
    - [2.7 Fair Composition of Self-Stabilizing Algorithms](book_2_7.md)
  - [2_5 极大匹配](note_2_5.md)
    - [2.9 Variant Function](book_2_9.md#variant-function)
    - [2.9 Example: Self-Stabilizing Maximal Matching](book_2_9.md#example-self-stabilizing-maximal-matching)
  - [2_6 一般通信网络中的领导者选举](note_2_6.md)
    - [2.9 Convergence Stairs](book_2_9.md#convergence-stairs)
    - [2.9 Example: Leader Election in a General Communication Network](book_2_9.md#example-leader-election-in-a-general-communication-network)
  - [2_7 完全图中的领导者选举](note_2_7.md)
    - [2.4 Randomized Self-Stabilization](book_2_4.md)
    - [2.9 Scheduler-Luck Game](book_2_9.md#scheduler-luck-game)
    - [2.9 Example: Self-Stabilizing Leader Election in Complete Graphs](book_2_9.md#example-self-stabilizing-leader-election-in-complete-graphs)
  - [3 没有自稳定的数据链路算法](note_3.md)
    - [3.1 Initialization of a Data-Link Algorithm in the Presence of Faults](book_3_1.md)
    - [3.2 Arbitrary Configuration Because of Crashes](book_3_2.md)
  - [4_1 模型转换](note_4_1.md)
    - [4.1 Token-Passing: Converting a Central Daemon to read/write](book_4_1.md)
    - [4.2 Data-Link Algorithms: Converting Shared Memory to Message Passing](book_4_2.md)
  - [4_2 排名算法](note_4_2.md)
    - [4.3 Self-Stabilizing Ranking: Converting an Id-based System to a Special-processor System](book_4_3.md)
  - [4_3 更新算法](note_4_3.md)
    - [4.4 Update: Converting a Special Processor to an Id-based Dynamic System](book_4_4.md)
  - [6_1 时钟同步](note_6_1.md)
    - [6.1 Digital Clock Synchronization](book_6_1.md)
  - [6_2 即使打盹也能自稳定](note_6_2.md)
    - [6.2 Stabilization in Spite of Napping](book_6_2.md)
  - 7.1 太简单就没有笔记了
