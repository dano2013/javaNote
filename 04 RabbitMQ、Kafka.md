# Kafka

### KafKa 基本特性

快速持久化、支持批量读写消息、支持消息分区，提高了并发能力、支持在线增加分区、支持为每个分区创建多个副本。

### 核心概念

- 生产者（Producer）： 生产消息，并且按照一定的规则推送到 Topic 的分区中；
- 消费者（Consumer）： 从 Topic 中拉去消息，并且进行消费；
- 主题（Topic）： 用于存储消息的逻辑概念，是一个消息集合；
- 分区（partition）：
  - 每个 Topic 可以划分为多个分区，每个消息在分区中都会有一个唯一编号 offset；
  - kafka 通过 offset 保证消息在分区中的顺序；
  - 同一 Topic 的不同分区可以分配在不同的 Broker 上；
  - partition 以文件的形式存储在文件系统中；

- 副本（replica）：
  - KafKa 对消息进行了冗余备份，每个分区有多个副本，每个副本中包含的消息是 “一样” 的；
  - 每个副本中都会选举出一个 Leader 副本，其余为 Follower 副本，Follower 副本仅仅将数据从 Leader 副本拉取到本地，然后同步到自己的 Log 中；

- 消费者组（Consumer Group）： 每个 consumer 都属于一个 consumer group，每条消息只能被 consumer group 中的一个 Consumer 消费，同时可以被多个 consumer group 消费；

- Broker：
  - 一个单独的 server 就是一个 Broker；
  - 主要工作是接收生产者发过来的消息，分配 offset，并且保存到磁盘中；

- Cluster&Controller：
  - 多个 Broker 可以组成一个 Cluster，每个集群选举一个 Broker 来作为 Controller，充当指挥中心；
  - Controller 负责管理每个分区的副本状态、监听 ZooKeeper 中数据的变化等工作；

- 保留策略和日志压缩：
  - 不管消费者是否已经消费了消息，KafKa 都会一直保存这些消息（持久化到磁盘）；
  - 通过保留策略，定时删除陈旧的消息；
  - 日志压缩，只保留最新的 Key-Value 对。

### 如何实现快速持久化

KafKa 将消息保存在磁盘中，并且读写磁盘的方式是顺序读写，避免了随机读写磁盘（寻道时间过长）导致的性能瓶颈；磁盘的顺序读写速度超过内存随机读写。

### 关于副本机制

ISR 集合 ：表示当前 “可用” 且消息量与 Leader 相差不多的副本集合。满足条件如下：

1. 副本所在节点必须维持着与 ZooKeeper 的连接；
2. 副本最后一条信息的 offset 与 Leader 副本的最后一条消息的 offset 之间的差值不能超过指定的阈值。

HW&LEO：

1. HW 标记了一个特殊的 offset，当消费者处理消息的时候，只能拉取到 HW 之前的消息；
2. HW 由 Leader 副本进行管理；
3. LEO（Log End Offset）是所有副本都会有的一个 offset 标记。

ISR、HW 和 LEO 的工作配合：

1. producer 向此分区中推送消息；
2. Leader 副本将消息追加到 Log 中，并且递增其 LEO；
3. Follower 副本从 Leader 副本中拉取消息进行同步；
4. Follower 副本将消息更新到本地 Log 中，并且递增其 LEO；
5. 当 ISR 集合中的所有副本都完成了对 offset 的消息同步，Leader 副本会递增其 HW。


