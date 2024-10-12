# note_3_没有自稳定的数据链路算法

包含 2.10 部分、3.1 和 3.2 内容和 PPT 内容。2.10 仅包括交替位算法，3.1 和 3.2 均舍弃了大部分的证明，仅留下了算法（或者说解决方案）。

包含：

- 2.10 部分：仅包括交替位算法
- 3.1 和 3.2 部分：舍弃了大部分的证明，仅留下了算法（或者说解决方案）

由于从这一部分开始，老师进行了有选择性的讲解，故而我也只能根据老师的节奏进行取舍；其次，从这里开始，如第 3 章和第 4 章，其行文结构比较复杂和相对的没有逻辑，而 PPT 上有着相当精炼的提纲辅助理解。

故而总的来说，之后的笔记将以 PPT 为导向。

---

注意这一章开头的两句话：

> there is no reliable data-link algorithm that can withstand processor crashes.
>
> crashes may cause the system to reach an arbitrary state.

事实证明，没有可靠的数据链路算法能够承受处理器崩溃。然后我们扩展上述结果，表明崩溃可能导致系统达到任意状态。

## 3.1 Initialization of a Data-Link Algorithm in the Presence of Faults

### 交替位算法

首先引入 2.10 中的交替位算法。书中讲述了一个比较流畅的运行过程。并有这样的结论，为后面的证明做基础：

> Once the sender receives an acknowledgment $<0>$, no frame with sequence number 1 exists in the system.
>
> 一旦发送方收到确认 $<0>$，系统中就不存在序列号为 1 的帧。

具体过程可以看 PPT。

### No Crash-Tolerant Data-link Algorithm

PPT 中有简练的结论说明，书中并没有。

There is No Crash-Tolerant Data-link Algorithm:

- It is usually assumed that a crash causes the sender/receiver to reach an initial state.
- Assuming asynchrony, unlimited channel capacity, and an undetectable crash/resume fault model with frame losses, there is **no initialization procedure exists such that we can guarantee that every message fetched by the sender, following the last crash, will arrive at its destination**.
- The next execution will demonstrate this point. Denote:
  - $Crash_R$ – receiver crash
  - $Crash_S$ – sender crash
- $Crash_X$ causes $X$ to perform an initialization procedure

## 3.2 Arbitrary Configuration Because of Crashes

### Arbitrary configuration because of crashes

A combination of:

- asynchrony,
- unlimited channel capacity, and
- undetectable crash-resume fault model with
- frame losses

can bring a system to any arbitrary states of processors and an arbitrary configuration.

### Crash-Resilient Data-Link Algorithm

这一部分逻辑比较复杂。下面是整体概述，具体请看书和 PPT。

崩溃不被认为是严重类型的故障。而从安全配置出发的之后一直保持安全的配置会被崩溃轻易击破，因为一致性无法保持。也就是说，确实没有自稳定的数据链路算法（我这里把它理解为 3.1 提到的 Crash-Tolerant Data-link Algorithm）。

但是当已知在传输中的帧数（传输时间）有上限时，存在一种抗崩溃的数据链路算法。该算法在发送方或接收方崩溃后使用初始化程序。它能确保交替位算法能正确初始化，并且发送方在最后一次崩溃后获取的任何消息都能被传递。

然后是初始化的具体过程，可以看 PPT。

我们需要保证一件事：在最后一次崩溃后恰好一次传递。

解决方案：

- 编程发送方在收到确认之前以标签 0 发送每条消息的帧，然后在收到确认之前以标签 1 发送相同的消息帧。
- 接收方仅在消息以标签 0 的帧到达后立即以标签 1 的帧到达时才传递消息。
  - 因此，如果发送方在 $R$ 崩溃后获取消息 $msg$，则必须在发送方开始发送 $<1, msg>$ 之前，接收方收到带有 $<0, msg>$ 的帧，确保 ${bit}_r$ 先设置为 0 然后设置为 1。
