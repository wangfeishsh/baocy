## 1. Getting Started

### 1.1 Introduction

Kafka is a distributed, partitioned, replicated commit log service. It provides the functionality of a messaging system, but with a unique design.

kafka 是一个分布式的，分区的，重复（or复制）的提交日志服务。它使用独特的设计，提供了消息系统的功能。

What does all that mean?

First let's review some basic messaging terminology术语:

* Kafka maintains feeds of messages in categories called _topics_.kafka维护的各种类别的信息源称为主题
* We'll call processes that publish messages to a Kafka topic _producers_.发布消息到一个kafka主题的过程，我们称之为生产者
* We'll call processes that subscribe to topics and process the feed of published messages _consumers_.订阅主题并且处理发布消息的信息源的过程，我们称之为消费者
* Kafka is run as a cluster comprised of one or more servers each of which is called a _broker_.kafka作为一个群集运行时包括一个或多个服务器，每个服务器都被称为代理服务器／掮客／经纪人

So, at a high level, producers send messages over the network to the Kafka cluster which in turn serves them up to consumers like this: 因此，在一个高的水平，生产者把消息通过网络发送到kafka集群，而这反过来又服务于消费者，如下图：

![](http://kafka.apache.org/images/producer_consumer.png)

Communication between the clients and the servers is done with a simple, high-performance, language agnostic  不可知论者 [TCP protocol](https://kafka.apache.org/protocol.html). We provide a Java client for Kafka, but clients are available in [many languages](https://cwiki.apache.org/confluence/display/KAFKA/Clients).

**Topics and Logs**

Let's first dive into the high-level abstraction Kafka provides—the topic.

A topic is a category or feed name to which messages are published. For each topic, the Kafka cluster maintains a partitioned log that looks like this:\( anatomy : 解剖，分解，分析; （详细的） 剖析 \)

![](http://kafka.apache.org/images/log_anatomy.png)

Each partition is an ordered, immutable sequence of messages that is continually appended to—a commit log. The messages in the partitions are each assigned a sequential id number called the _offset_ that uniquely identifies each message within the partition.

每个分区是一个不断追加有序的，不变的序列的信息的提交日志。分区中的每个消息分配一个序列号被称偏移量，用来作这条消息在分区中的唯一标识。

The Kafka cluster retains all published messages—whether or not they have been consumed—for a configurable period of time. For example if the log retention is set to two days, then for the two days after a message is published it is available for consumption, after which it will be discarded to free up space. Kafka's performance is effectively constant with respect to data size so retaining lots of data is not a problem.

Kafka集群保留所有已发布的消息，无论它们是否已经被消费了，在一段可配置的时间内。例如，如果日志保留被设置为两天，那么在一个消息发布后的两天，它是可用的，随后它将被丢弃以便释放空间。相对于数据的大小，Kafka的性能实际上是常量，所以保留了大量的数据是不是一个问题。

In fact the only metadata retained on a per-consumer basis is the position of the consumer in the log, called the "offset". This offset is controlled by the consumer: normally a consumer will advance its offset linearly as it reads messages, but in fact the position is controlled by the consumer and it can consume messages in any order it likes. For example a consumer can reset to an older offset to reprocess.

事实上，唯一的元数据保留在每一个消费的基础上是消费者在日志中的位置，称为“偏移”。这种偏移是由消费者控制：通常当它读取消息，一个消费者将线性的提前其偏移量。但实际上该位置是由消费者控制，它可以按照想要的任意顺序消费消息。例如，一个消费者可以重置为一个旧的的偏移去重新处理。

This combination of features means that Kafka consumers are very cheap—they can come and go without much impact on the cluster or on other consumers. For example, you can use our command line tools to "tail" the contents of any topic without changing what is consumed by any existing consumers.

这种内置功能，意味着Kafka的消费者非常便利，他们可以来和去，对集群或其他消费者没有太大的影响。例如，您可以使用我们的命令行工具“tail”的任何主题的内容，而不改变任何现有的消费者所消费的消息。

The partitions in the log serve several purposes. First, they allow the log to scale beyond a size that will fit on a single server. Each individual partition must fit on the servers that host it, but a topic may have many partitions so it can handle an arbitrary amount of data. Second they act as the unit of parallelism—more on that in a bit.

日志服务中分区的几个目的。首先，他们允许日志规模的大小超出了在一个单一的服务器上的大小。每个单独的分区都必须适于各自主机，但一个主题可能有许多分区，所以它可以处理任意数量的数据。第二，他们作为平行的单元，胜于在一点。

**Distribution**

The partitions of the log are distributed over the servers in the Kafka cluster with each server handling data and requests for a share of the partitions. Each partition is replicated across a configurable number of servers for fault tolerance.

Each partition has one server which acts as the "leader" and zero or more servers which act as "followers". The leader handles all read and write requests for the partition while the followers passively replicate the leader. If the leader fails, one of the followers will automatically become the new leader. Each server acts as a leader for some of its partitions and a follower for others so load is well balanced within the cluster.

**Producers**

Producers publish data to the topics of their choice. The producer is responsible for choosing which message to assign to which partition within the topic. This can be done in a round-robin fashion simply to balance load or it can be done according to some semantic partition function \(say based on some key in the message\). More on the use of partitioning in a second.

生产者将数据发布到他们所选择的主题。生产者负责将哪个消息分配到主题的哪个分区中。这可以以循环的方式简单的负载均衡也可以根据一些语义分区函数所做的（意味着根据消息中的一些键）。分区更多使用第二种方式。

**Consumers**

Messaging traditionally has two models: [queuing](http://en.wikipedia.org/wiki/Message_queue) and [publish-subscribe](http://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern). In a queue, a pool of consumers may read from a server and each message goes to one of them; in publish-subscribe the message is broadcast to all consumers. Kafka offers a single consumer abstraction that generalizes both of these—the _consumer group_.

Consumers label themselves with a consumer group name, and each message published to a topic is delivered to one consumer instance within each subscribing consumer group. Consumer instances can be in separate processes or on separate machines.

消费者通过消费者的组名来标记自己，每个消息发布到主题，然后再传递到每个订阅的消费组的一个消费者实例。消费者实例可以在不同的程序或单独的机器上。

If all the consumer instances have the same consumer group, then this works just like a traditional queue balancing load over the consumers.

If all the consumer instances have different consumer groups, then this works like publish-subscribe and all messages are broadcast to all consumers.

More commonly, however, we have found that topics have a small number of consumer groups, one for each "logical subscriber". Each group is composed of many consumer instances for scalability and fault tolerance. This is nothing more than publish-subscribe semantics where the subscriber is a cluster of consumers instead of a single process.

![](http://kafka.apache.org/images/consumer-groups.png)

_A two server Kafka cluster hosting four partitions \(P0-P3\) with two consumer groups. Consumer group A has two consumer instances and group B has four. _

Kafka has stronger ordering guarantees than a traditional messaging system, too.

并且Kafka比传统的消息系统有更强的顺序保证。

A traditional queue retains messages in-order on the server, and if multiple consumers consume from the queue then the server hands out messages in the order they are stored. However, although the server hands out messages in order, the messages are delivered asynchronously to consumers, so they may arrive out of order on different consumers. This effectively means the ordering of the messages is lost in the presence of parallel consumption. Messaging systems often work around this by having a notion of "exclusive consumer" that allows only one process to consume from a queue, but of course this means that there is no parallelism in processing.

传统的队列在服务器上按顺序保留消息，如果多个消费者从队列中消费，然后服务器将它们存储的消息按顺序发送出去。然而，虽然服务器按顺序发送消息，但消息被异步发送给消费者，所以他们可能会在不同的消费者上出现不同顺序。这明显地意味着在并行消费的情况下，消息的顺序丢失。消息系统经常围绕这个工作，有一个“独家消费者”的概念，只允许一个进程从一个队列中消耗，但当然这意味着在处理中没有并行性。

Kafka does it better. By having a notion of parallelism—the partition—within the topics, Kafka is able to provide both ordering guarantees and load balancing over a pool of consumer processes. This is achieved by assigning the partitions in the topic to the consumers in the consumer group so that each partition is consumed by exactly one consumer in the group. By doing this we ensure that the consumer is the only reader of that partition and consumes the data in order. Since there are many partitions this still balances the load over many consumer instances. Note however that there cannot be more consumer instances in a consumer group than partitions.

Kafka做得更好。通过主题内一个概念的并行的－－分区，Kafka是能够通过消费者池提供排序保证和负载均衡。这是通过分配主题的分区至消费组中的消费者，使每个分区被该组中的一个消费者消费。通过这样做，我们确保此消费者是此分区的唯一的读者，并按顺序消费数据。因此众多分区，通过许多消费实例均衡负载。请注意，在一个消费组中，消费者实例不能多于分区。

Kafka only provides a total order over messages _within_ a partition, not between different partitions in a topic. Per-partition ordering combined with the ability to partition data by key is sufficient for most applications. However, if you require a total order over messages this can be achieved with a topic that has only one partition, though this will mean only one consumer process per consumer group.

Kafka只为在一个分区中的消息提供了一个总的顺序，而不是在一个主题的不同分区之间的。对于大多数应用，分区排序结合分区数据键的能力，就已经足够了。然而，如果您需要一个消息的总顺序，这可以通过一个主题只有一个分区实现，虽然这将意味着每一个消费组只有一个消费者进程。

**Guarantees**

At a high-level Kafka gives the following guarantees:

* Messages sent by a producer to a particular topic partition will be appended in the order they are sent. That is, if a message M1 is sent by the same producer as a message M2, and M1 is sent first, then M1 will have a lower offset than M2 and appear earlier in the log.
* A consumer instance sees messages in the order they are stored in the log.
* For a topic with replication factor N, we will tolerate up to N-1 server failures without losing any messages committed to the log.

More details on these guarantees are given in the design section of the documentation.

* 由生产者发送到一个特定主题分区的消息将被按照发送的顺序追加到日志中。那就是，如果一个消息M1发送同样的消息生产者为M2，M1发送第一，然后M1将有一个比M2低偏移和出现在日志前。

  一个消费实例在存储在日志中的顺序中看到消息。

  对于一个主题的复制因子N，我们会容忍n-1服务器故障而不丢失任何信息提交到日志。



