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

Kafka集群保留所有已发布的消息，无论它们是否已经被消费了，在一段可配置的时间内。例如，如果日志保留被设置为两天，那么在一个消息发布后的两天，它是可用的，它将被丢弃到空闲空间。卡夫卡的性能是有效的常数相对于数据的大小，所以保留了大量的数据是不是一个问题。

In fact the only metadata retained on a per-consumer basis is the position of the consumer in the log, called the "offset". This offset is controlled by the consumer: normally a consumer will advance its offset linearly as it reads messages, but in fact the position is controlled by the consumer and it can consume messages in any order it likes. For example a consumer can reset to an older offset to reprocess.

事实上，唯一的元数据保留在每一个消费的基础上是消费者在日志中的位置，称为“偏移”。这种偏移是由消费者控制：通常当它读取消息，一个消费者将线性的提前其偏移量。但实际上该位置是由消费者控制，它可以按照想要的任意顺序消费消息。例如，一个消费者可以重置为一个旧的的偏移去重新处理。

This combination of features means that Kafka consumers are very cheap—they can come and go without much impact on the cluster or on other consumers. For example, you can use our command line tools to "tail" the contents of any topic without changing what is consumed by any existing consumers.

The partitions in the log serve several purposes. First, they allow the log to scale beyond a size that will fit on a single server. Each individual partition must fit on the servers that host it, but a topic may have many partitions so it can handle an arbitrary amount of data. Second they act as the unit of parallelism—more on that in a bit.
