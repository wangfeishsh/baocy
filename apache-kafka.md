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

The Kafka cluster retains all published messages—whether or not they have been consumed—for a configurable period of time. For example if the log retention 保留 is set to two days, then for the two days after a message is published it is available for consumption, after which it will be discarded to free up space. Kafka's performance is effectively constant with respect to data size so retaining lots of data is not a problem.

In fact the only metadata retained on a per-consumer basis is the position of the consumer in the log, called the "offset". This offset is controlled by the consumer: normally a consumer will advance its offset linearly as it reads messages, but in fact the position is controlled by the consumer and it can consume messages in any order it likes. For example a consumer can reset to an older offset to reprocess.

This combination of features means that Kafka consumers are very cheap—they can come and go without much impact on the cluster or on other consumers. For example, you can use our command line tools to "tail" the contents of any topic without changing what is consumed by any existing consumers.

The partitions in the log serve several purposes. First, they allow the log to scale beyond a size that will fit on a single server. Each individual partition must fit on the servers that host it, but a topic may have many partitions so it can handle an arbitrary amount of data. Second they act as the unit of parallelism—more on that in a bit.

