# note_2_6_一般通信网络中的领导者选举

包含 2.9 章部分内容和 PPT 内容。

## 领导者选举实际应用

Leader election algorithms are fundamental in computer networks and distributed systems for various applications:

1. **Coordinator Election:** In distributed systems, leader election helps select a coordinator or leader node among a group of nodes. The leader node often acts as a central point for decision-making or coordination.

2. **Load Balancing:** Leader election can be used for load balancing in distributed systems. The leader node may distribute incoming requests or tasks evenly among available nodes, helping to optimize resource utilization.

3. **Cluster Management:** In clustered systems or cloud environments, leader election can help manage clusters of nodes efficiently. The leader node may coordinate resource allocation, scaling, and maintenance tasks within the cluster.

4. **Network Routing:** In computer networks, leader election can be used to select a designated router or coordinator node responsible for routing decisions. In protocols like OSPF (Open Shortest Path First), leader election determines the router responsible for maintaining the routing tables.

5. **Mobile Ad Hoc Networks (MANETs):** In MANETs, leader election can help determine a temporary leader or coordinator node responsible for maintaining network stability and organization, especially in dynamic and self-configuring networks.

6. **Sensor Networks:** Leader election is crucial in sensor networks for selecting a leader node that aggregates data from multiple sensors, conserves energy by controlling sensor activity, and communicates with a central server.

7. **IoT (Internet of Things):** In IoT deployments, leader election can be used to select edge devices or gateways responsible for data aggregation, preprocessing, and transmission to cloud services.

8. **Decentralized Systems:** In peer-to-peer (P2P) networks or blockchain networks, leader election plays a role in consensus algorithms. Nodes participate in leader election to determine who has the authority to create new blocks, validate transactions, or perform consensus tasks.

9. **Resource Allocation:** In scenarios where, multiple nodes compete for limited resources, leader election can help select the node responsible for resource allocation, ensuring fairness and efficiency.

Leader election algorithms are a crucial building block for achieving fault tolerance, load balancing, and efficient coordination in distributed systems and computer networks. They play a vital role in maintaining system stability and resilience, especially in large-scale and complex distributed environments.

## 简单的（非终止）非自稳定算法

> 以下简单的（非终止）算法以非稳定的方式选举领导者。
>
> - 每个处理器 $P_i$ 都有一个领导者候选人；一开始，**候选人是 $P_i$ 自己**。
>
> - $P_i$ 反复将其当前候选人的标识符 $x$ 传达给其邻居。
> - 每当 $P_i$ 收到邻居候选人的标识符 $y$ 时，如果 $x > y$，$P_i$ 会将其候选人更改为标识符为 $y$ 的处理器。
>
> 算法不是自稳定的，因为在系统的第一个（任意）配置中，最小标识符 $z$ 可能不是系统中某个处理器的标识符。
>
> **术语 *浮动标识符* 用于描述在初始配置中出现的标识符，而系统中没有处理器具有该标识符**。

为什么算法不是自稳定的？

- 因为这个算法是选举标识符最小的领导者，也就是说在互相比较的过程中会不断变小，因此一开始可能会设置一个比较大的值；
- 但是他并没有规定这个值的范围，故而可能是大于 $N$ 的；
- 那么就有这样一种可能，其中最小的，也即最小标识符 $z$ 就不是系统中某个处理器的标识符。
- 那么你虽然通过算法得到了最小标识符，但是它不在系统内，没有意义。

---

额外的，和后面的算法比起来，你会发现不涉及到领导者的距离的选举没有现实意义。

## 领导者选举算法

> 处理器 $P_i$ 反复读取其邻居为领导者选择的标识符。$P_i$ 选择读取值中最小的标识符 $x$，使得到 $x$ 的距离小于 $N$。如果 $y$ 是从邻居读取的最小距离以及标识符 $x$，那么 $P_i$ 将 $y+1$ 分配给其距离字段。

这里就很清楚，首先这个距离要小于 $N$，也就是保证合法性。

### 正确性证明

我不明白这里对于浮动标识符的消除的证明。如果一开始所有处理器的标识符都大于 N，那么这个算法无法进行下去。
