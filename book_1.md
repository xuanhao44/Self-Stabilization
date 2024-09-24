# 1 Introduction

The self-stabilization paradigm was introduced by Edsger W. Dijkstra in 1973. A self-stabilizing system is a system that can automatically recover following the occurrence of (transient) faults. The idea is to design systems that can be started in an arbitrary state and still converge to a desired behavior. The occurrence of faults can cause the system to reach an arbitrary state. Self stabilizing systems that experience faults recover automatically from such faults, since they are designed to start in an arbitrary state.

自稳定范式由 Edsger W. Dijkstra 于 1973 年提出。自稳定系统是在发生（暂态）故障后能够自动恢复的系统。其思想是设计可以在任意状态下启动并仍然收敛到期望行为的系统。故障的发生可能导致系统达到任意状态。经历故障的自稳定系统会自动从这种故障中恢复，因为它们被设计为在任意状态下启动。

The craft of designing self-stabilizing distributed algorithms is challenging and exciting, as Edsger W. Dijkstra discovered in designing the first self stabilizing algorithm. Just before presenting the details of the algorithm, Dijkstra challenges the readers to stop reading and design a self-stabilizing algorithm for mutual exclusion by themselves. It is not only curiosity and intellectual challenge, however, that motivate the extensive study of self-stabilization; the major reason for this wide interest is the applicability of self-stabilizing algorithms to distributed systems.

正如 Edsger W. Dijkstra 在设计第一个自稳定算法时所发现的那样，设计自稳定分布式算法的工艺既具有挑战性又令人兴奋。就在介绍算法细节之前，Dijkstra 要求读者停止阅读，自己设计一个互斥的自稳定算法。然而，不仅仅是好奇心和智力上的挑战激发了对自我稳定的广泛研究；引起这种广泛兴趣的主要原因是自稳定算法对分布式系统的适用性。

Existing algorithms such as routing algorithms are designed to stabilize starting from any arbitrary initial state. In a large, distributed, heterogeneous communication network, it is hard to predict in advance the exact combination of failures that will occur or their result. The self-stabilization property handles such a complicated situation by assuming an arbitrary initial state. From any arbitrary initial state, the system should recover and exhibit the desired behavior. For example, it is important that the control system of an autonomous vehicle such as, say, a space shuttle be designed to self-stabilize. The space shuttle may experience a fault — e.g., a power supply problem — and will have to recover automatically. If a space shuttle experiences a fault because of a momentary electrical power problem, it may malfunction for a while, but it will not be lost as long as it uses a self-stabilizing algorithm for its control, since this algorithm will cause the control to recover automatically and continue in its task.

现有的算法，如路由算法，被设计为稳定从任意初始状态出发。在一个大型的、分布式的、异构的通信网络中，很难提前预测将要发生的故障的确切组合或它们的结果。自稳定特性通过假设任意初始状态来处理这种复杂的情况。从任意的初始状态，系统应该恢复并表现出期望的行为。例如，像航天飞机这样的自主飞行器的控制系统必须设计成自稳定的，这一点很重要。航天飞机可能出现故障——例如电源问题——必须自动恢复。如果航天飞机因为一时的电力问题而发生故障，可能会出现一段时间的故障，但只要使用自稳定算法进行控制，就不会丢失，因为该算法会使控制自动恢复并继续执行任务。

To gain some idea of what a self-stabilizing algorithm is, let us use a pictorial example that we call “the stabilizing orchestra example.” On a windy evening, an orchestra is invited to play outdoors. Unfortunately, the conductor is unable to participate. Players listen to the players who are sitting nearby in order to play in harmony. The orchestra is required to play the same piece of music over and over again. Each player has a score to follow in the performance, starting from the beginning of the score again whenever he or she finishes playing the last page.

为了了解什么是自稳定算法，让我们使用一个形象的例子，我们称之为”稳定管弦乐队示例”。在一个有风的晚上，一个管弦乐队被邀请到户外演奏。不幸的是，指挥不能参加。乐团成员通过听坐在附近的其他成员来保持和谐演奏。乐团被要求一遍又一遍地演奏同一首乐曲。每个成员都有一个乐谱，在演奏完最后一页后，从头开始重新演奏。

Since the orchestra is playing outdoors, the wind can, from time to time, turn some of the pages in the score; moreover, the players may not even notice the change. We would like to guarantee that harmony is achieved at some point following the last such undesired page turn. It seems that the demand that harmony be achieved at some point following the last undesired page change is too weak; what happens if the wind continues turning the pages forever, but there are long periods in which no change occurs? Would the players be playing out of synchrony all the time? Fortunately, the players do not know whether a page change is the last page change, so our demand forces them to achieve synchrony during the long periods in which the wind does not turn the pages. Later harmony may be repeatedly lost again due to page changes, and then repeatedly achieved in every long enough period of time.

由于乐团在户外演奏，风可能会不时地翻动乐谱中的一些页面；而且，乐团成员可能甚至不会注意到这种变化。我们希望确保在最后一次这种不希望发生的翻页之后，乐团能够达到和谐。看起来，要求在最后一次不希望发生的翻页之后达到和谐的要求太弱了；如果风一直不停地翻页，但有很长一段时间没有变化，会发生什么呢？乐团成员会一直不同步地演奏吗？幸运的是，乐团成员不知道某次翻页是否是最后一次翻页，因此我们的要求迫使他们在风不翻页的长时间内实现同步。之后由于翻页，和谐可能会反复丧失，但在每一个足够长的时间段内又会再次实现和谐。

Imagine that you are the drummer and you find out that each of the violin players sitting near you is playing a different page. What should you do? Since your page may be turned without your noticing, you cannot stick with your current page. So, should you turn to the same page that one of the neighboring violinists is currently playing? Note that it is possible that at the same moment this violin player may change to your page and start playing from the place you are currently playing.

想象一下，你是鼓手，你发现坐在你附近的每个小提琴手都在演奏不同的页面。你应该怎么做呢？由于你的页面可能在你不注意的情况下被翻动，你不能坚持当前的页面。那么，你是否应该转到其中一个邻近小提琴手正在演奏的页面呢？请注意，很可能在同一时刻，这个小提琴手也会转到你的页面，并从你当前演奏的地方开始演奏。

Maybe once asynchrony is detected, a player should start playing from the beginning of the score. In such a case, the drummer and the violin players start simultaneously from the beginning of the score, but a cello player, who is next to the violin player and who playing in synchrony with the violin, may still continue playing, and will restart only after recognizing that the violin player has restarted. Therefore, both the cello and the violin players will restart playing while the drummer continues. This scenario may repeat itself.

也许一旦检测到不同步，演奏者应该从乐谱的开头开始演奏。在这种情况下，鼓手和小提琴手会同时从乐谱的开头开始演奏，但一个与小提琴手同步演奏的大提琴手可能会继续演奏，直到他注意到小提琴手已经重新开始演奏。因此，大提琴手和小提琴手都会重新开始演奏，而鼓手则继续演奏。这种情况可能会反复发生。

Now we are ready to present our first self-stabilizing solution. A self stabilizing solution would be for every player to join the neighboring player who is playing the earliest page, if this page is earlier than his or her own page. Assuming the scores are long enough, this strategy will reestablish synchrony following the last undesired page change, as shown by the following line of reasoning. Let us assume for now that no player finishes the score and turns to its first page before synchrony is achieved. In such a case, we can find a player who is playing a page with the minimal page number, and this player will cause all the other players to join him: the neighbors of the player who is playing the page with the minimal number will join this player. Once these neighbors play simultaneously, the neighbors of these neighbors will change page and join them, and eventually all players will be playing in harmony.

现在我们准备介绍我们的第一个自稳定解决方案。一个自稳定的解决方案是，每个演奏者都加入正在演奏最早页面的邻近演奏者，如果这个页面比他或她自己的页面早。假设乐谱足够长，这个策略将在最后一次不希望发生的翻页之后重新建立同步，如以下推理所示。现在让我们假设在同步实现之前，没有演奏者完成乐谱并翻到第一页。在这种情况下，我们可以找到一个正在演奏最小页码的演奏者，这个演奏者会让所有其他演奏者加入他：演奏最小页码的演奏者的邻居会加入他。一旦这些邻居同时演奏，这些邻居的邻居也会换页并加入他们，最终所有演奏者都会和谐地演奏。

The case still to be considered is that in which a player goes to the first page of the score before harmony is achieved. This case is discussed in detail in the sequel; at this stage, we give only a sketch of the argument that the reader may choose to skip. It is not possible to argue from some player who is playing the minimal-number page, since some other player starts playing an earlier page later on. However, if the score is long enough, we can argue that all the players will be playing a page in the beginning of the book some time after a player plays the first page. Let us say that the drummer is a player who changed pages to the first page; the neighbors of the drummer will change pages to one of the first pages, as will the neighbors of the neighbors, and so on. Once every player is playing one of the first pages and no player will change pages to the first page for a long enough time, we can therefore use the claims of the first case to convince ourselves that harmony is achieved.

还需要考虑的情况是，在和谐实现之前，有演奏者翻到了乐谱的第一页。这种情况在后续部分会详细讨论；在这个阶段，我们只给出一个论点的概要，读者可以选择跳过。不能从某个演奏最小页码的演奏者开始论证，因为其他演奏者可能会在稍后开始演奏更早的页面。然而，如果乐谱足够长，我们可以论证，在某个演奏者翻到第一页之后，所有演奏者都会在某个时间点演奏书的开头几页。假设鼓手是翻到第一页的演奏者；鼓手的邻居会翻到前几页之一，鼓手邻居的邻居也会如此，依此类推。一旦每个演奏者都在演奏前几页，并且没有演奏者在足够长的时间内翻到第一页，我们就可以用第一种情况的论点来说服自己，和谐已经实现。

We can conclude that in every long enough period in which the wind does not turn a page, the orchestra resumes playing in synchrony. This is the spirit of self-stabilization — coping with transient faults by designing an algorithm that ensures restablishment of a desired property. In the orchestra case, the transient faults are the wind’s page turns and the desired property is the synchronization among the players. After the last transient fault, an arbitrary situation is reached where each player may be playing a different page. From every such arbitrary situation, the orchestra reaches a stage after which it is playing in harmony.

我们可以得出结论，在每一个足够长的时间段内，如果风没有翻页，乐团就会恢复同步演奏。这就是自稳定的精神——通过设计一种算法来应对瞬时故障，确保重新建立所需的特性。在乐团的例子中，瞬时故障是风翻动页面，而所需的特性是演奏者之间的同步。在最后一次瞬时故障之后，会出现一个任意的情况，每个演奏者可能在演奏不同的页面。从每一个这样的任意情况开始，乐团最终会达到一个阶段，在这个阶段之后，它将和谐地演奏。

Note that, although the algorithm is guaranteed to achieve synchrony after the last transient fault, the audience may still notice the disturbance that the wind causes (occurrence of the transient faults).

请注意，尽管算法保证在最后一次瞬时故障后实现同步，观众仍可能注意到风引起的干扰（瞬时故障的发生）。

Let us continue with few historical notes. Research on self-stabilizing algorithms started with the pioneering work of Edsger W. Dijkstra in 1973. Dijkstra demonstrated the new self-stabilization concept by presenting self stabilizing mutual exclusion algorithms. Dijkstra’s work was not widely noticed until Leslie Lamport’s invited talk at the ACM Symposium on Principles of Distributed Computing in 1983. In his report on Dijkstra’s work on self stabilization, Leslie Lamport stated:

让我们继续一些历史注释。自稳定算法的研究始于 1973 年 Edsger W. Dijkstra 的开创性工作。Dijkstra 通过提出自稳定互斥算法展示了新的自稳定概念。Dijkstra 的工作直到 1983 年 Leslie Lamport 在 ACM 分布式计算原理研讨会上的特邀演讲才引起广泛关注。在他关于 Dijkstra 自稳定工作的报告中，Leslie Lamport 指出：

I regard this as Dijkstra’s most brilliant work — at least, his most brilliant published paper. It’s almost completely unknown. I regard it to be a milestone in work on fault tolerance... I regard self-stabilization to be a very important concept in fault tolerance, and to be a very fertile field for research.

“我认为这是 Dijkstra 最杰出的工作——至少是他最杰出的已发表论文。它几乎完全不为人知。我认为这是容错工作中的一个里程碑……我认为自稳定是容错中的一个非常重要的概念，也是一个非常富有研究潜力的领域。

As often happens with Lamport’s predictions, self-stabilization research is one of the most active research fields in distributed computing. Innovative theoretical results that can be used in practice have been presented in recent years. Distributed algorithms that were successfully implemented have now been identified as self-stabilizing. Self-stabilization is a concept of interest to both theoreticians and practitioners.

正如经常发生在 Lamport 预测中的那样，自稳定研究是分布式计算中最活跃的研究领域之一。近年来，已经提出了可以在实践中使用的创新理论成果。成功实施的分布式算法现在已被确定为自稳定的。自稳定是理论家和实践者都感兴趣的一个概念。

The book starts with the description of basic definitions and techniques to gain basic understanding of what self-stabilization is. Then in chapter 3, using our basic familiarity with self-stabilization, we prove that there are frequent cases in which only self-stabilizing algorithms can automatically resume operation. Chapter 3 concludes with some frequently asked questions.

本书从描述基本定义和技术开始，以便对自稳定有一个基本的理解。然后在第三章中，利用我们对自稳定的基本熟悉，我们证明了在许多情况下，只有自稳定算法才能自动恢复操作。第三章以一些常见问题的解答作为结尾。

The remaining chapters are ordered according to their sophistication, starting with basic techniques and continuing with more advanced topics. Chapter 4 is dedicated to the description of the different distributed systems for which self-stabilizing algorithms exist. In fact, it uses the excuse of converting self stabilizing algorithms (to be executed by different types of distributed systems) to present several fundamental self-stabilizing algorithms. Then in chapter 5 techniques for converting non-stabilizing algorithms to self-stabilizing algorithms are presented. Chapter 6 uses digital clock synchronization task to demonstrate the feasibility of coping with a combination of transient faults and other types of faults. Chapter 7 presents techniques concerned with algorithm behavior during stabilization. In particular, the techniques in chapter 7 ensure fast stabilization when only a few of the processors experience transient faults. The last chapter explores the case of a single machine modeled by a Turing machine.

剩余的章节按其复杂程度排列，从基本技术开始，然后继续讨论更高级的主题。第四章专门描述了存在自稳定算法的不同分布式系统。实际上，它利用将自稳定算法转换为由不同类型的分布式系统执行的借口，介绍了几种基本的自稳定算法。然后在第五章中，介绍了将非自稳定算法转换为自稳定算法的技术。第六章使用数字时钟同步任务来展示应对瞬时故障和其他类型故障组合的可行性。第七章介绍了与算法在稳定期间行为有关的技术。特别是，第七章中的技术确保当只有少数处理器经历瞬时故障时，能够快速稳定。最后一章探讨了由图灵机建模的单台机器的情况。

Notes

The first self-stabilizing algorithms were presented in [Dij73] (See also [Dij74; Dij86]). Three self-stabilizing algorithms for mutual exclusion in a ring are presented in [Dij73]. The invited talk of Leslie Lamport can be found in [Lam83].

第一个自稳定算法在 [Dij73] 中提出 (另见[Dij74;Dij86])。在[Dij73] 中提出了环上互斥的三种自稳定算法。Leslie Lamport 的受邀演讲可以在 [Lam83] 中找到。
