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

事实上，唯一的元数据保留在每一个消费者的基础上是消费者在日志中的位置，称为“偏移”。这种偏移是由消费者控制：通常当它读取消息， offset将会"线性"的向前驱动（ 即消息将依次顺序被消费 ） 。 事实上consumer可以使用任意顺序消费消息,它只需要将offset重置为任意值 。例如，一个消费者可以重置为一个旧的的偏移去重新处理。

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

* 由生产者发送到一个特定主题分区的消息将被按照发送的顺序追加到日志中。也就是，如果一个消息M1与M2由同一生产者发送，M1为第一发送，然后M1将有一个比M2低偏移量出现在日志前。

* 一个消费实例看到消息的顺序与在消息存储在日志中的顺序一致。

* 对于一个主题的复制因子N，我们会容忍N-1服务器故障而不丢失任何信息提交到日志。


### 1.2 Use Cases

Here is a description of a few of the popular use cases for Apache Kafka. For an overview of a number of these areas in action, see [this blog post](http://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying).

**Messaging**

Kafka works well as a replacement for a more traditional message broker. Message brokers are used for a variety of reasons \(to decouple processing from data producers, to buffer unprocessed messages, etc\). In comparison to most messaging systems Kafka has better throughput, built-in partitioning, replication, and fault-tolerance which makes it a good solution for large scale message processing applications.

In our experience messaging uses are often comparatively low-throughput, but may require low end-to-end latency and often depend on the strong durability guarantees Kafka provides.

In this domain Kafka is comparable to traditional messaging systems such as [ActiveMQ](http://activemq.apache.org/) or [RabbitMQ](https://www.rabbitmq.com/).

Kafka更像是一个传统的信息broker的替代。信息brokers是应用于各种原因（分离数据生产者，缓冲未处理的消息等）。与大多数消息系统相比，Kafka有更好的吞吐量，内置的分区，复制和容错性，这使得它是一个很好的为大规模的消息处理应用的解决方案。

在我们的经验消息使用往往是比较低的吞吐量，但可能需要低的终端到终端的延迟，往往依赖于kafka提供强大的持久性保证。

在这一领域的Kafka与传统的消息传递系统如ActiveMQ和RabbitMQ。

**Website Activity Tracking** 网站活性跟踪

The original use case for Kafka was to be able to rebuild a user activity tracking pipeline as a set of real-time publish-subscribe feeds. This means site activity \(page views, searches, or other actions users may take\) is published to central topics with one topic per activity type. These feeds are available for subscription for a range of use cases including real-time processing, real-time monitoring, and loading into Hadoop or offline data warehousing systems for offline processing and reporting.

Activity tracking is often very high volume as many activity messages are generated for each user page view.

Kafka的原始用例是能够重建一个用户活动跟踪管道作为一组实时发布订阅。这意味着站点活动（用户可能的页面视图、搜索或其他操作）将被发布到每个活动类型的一个主题的中心主题中。这些feeds可用于一系列的使用情况，包括实时处理、实时监控，和加载到Hadoop或离线数据仓库系统用来离线处理和报告。

活动跟踪通常是高容量的，因为每个用户页面视图都会生成多个活动消息。

**Metrics**度量标准

Kafka is often used for operational monitoring data. This involves aggregating statistics from distributed applications to produce centralized feeds of operational data.

Kafka经常被用于操作监控数据。这涉及到从分布式应用程序汇总统计数据，以产生操作数据的集中源。

**Log Aggregation 日志集成**

Many people use Kafka as a replacement for a log aggregation solution. Log aggregation typically collects physical log files off servers and puts them in a central place \(a file server or HDFS perhaps\) for processing. Kafka abstracts away the details of files and gives a cleaner abstraction of log or event data as a stream of messages. This allows for lower-latency processing and easier support for multiple data sources and distributed data consumption. In comparison to log-centric systems like Scribe or Flume, Kafka offers equally good performance, stronger durability guarantees due to replication, and much lower end-to-end latency.

许多人使用Kafka作为一个日志集成解决方案的替代。日志聚合通常从服务器收集物理日志文件放在一个中心位置（文件服务器或HDFS也许）处理。Kafka抽象了文件细节，并给出了一个干净的抽象的日志或事件数据作为一个流的消息。这允许低延迟处理和更容易支持多个数据源和分布式数据消费。与以日志为中心的系统做比较，如Scribe或Flume，Kafka提供同样良好的性能，由复制带来更强的持久性保证，和终端到终端的低延迟。

**Stream Processing**

Many users of Kafka process data in processing pipelines consisting of multiple stages, where raw input data is consumed from Kafka topics and then aggregated, enriched, or otherwise transformed into new topics for further consumption or follow-up processing. For example, a processing pipeline for recommending news articles might crawl article content from RSS feeds and publish it to an "articles" topic; further processing might normalize or deduplicate this content and published the cleansed article content to a new topic; a final processing stage might attempt to recommend this content to users. Such processing pipelines create graphs of real-time data flows based on the individual topics. Starting in 0.10.0.0, a light-weight but powerful stream processing library called Kafka Streams is available in Apache Kafka to perform such data processing as described above. Apart from Kafka Streams, alternative open source stream processing tools include [Apache Storm](https://storm.apache.org/) and [Apache Samza](http://samza.apache.org/).

许多Kafka用户处理数据时，在整个处理管道中包含了多个阶段，其中从Kafka主题的原始输入数据消费，然后汇总，丰富，或以其他方式转化为新的主题进一步消费或后续处理。例如，一个推荐新闻文章的处理管道会从RSS订阅抓取文章内容并发布到“文章”的话题；进一步处理应该规范或去重复内容，然后发布洁净文章内容为一个新主题；最后一个处理阶段可能会尝试推荐内容给用户。这样的处理管道创建基于单个主题的实时数据流的图表。从0.10.0.0，一个轻量级但功能强大的流处理library称Kafka Streams可以在Apache Kafka履行上述数据处理。除了Kafka Streams，还有开源的流处理工具包括Apache Storm 与Apache samza可供选择

**Event Sourcing**

[Event sourcing](http://martinfowler.com/eaaDev/EventSourcing.html) is a style of application design where state changes are logged as a time-ordered sequence of records. Kafka's support for very large stored log data makes it an excellent backend for an application built in this style.

Event sourcing是一种应用程序设计的风格，其中状态更改被记录为一个时间顺序的序列记录。Kafka支持非常大的存储日志数据，使它成为一个优秀的内置这种风格的后台应用程序。

**Commit Log**

Kafka can serve as a kind of external commit-log for a distributed system. The log helps replicate data between nodes and acts as a re-syncing mechanism for failed nodes to restore their data. The [log compaction](http://kafka.apache.org/documentation.html#compaction) feature in Kafka helps support this usage. In this usage Kafka is similar to [Apache BookKeeper](http://zookeeper.apache.org/bookkeeper/) project.

Kafka可以作为一种分布式系统的外部提交日志。日志可以在节点间复制复制数据并使用重新同步机制恢复失效节点的数据。Kafka的日志压缩功能支持这种用法。这种用法类似于Apache Bookeerper项目。

### 1.3 Quick Start

This tutorial assumes you are starting fresh and have no existing Kafka or ZooKeeper data.

......

Now create a new topic with a replication factor of three:

&gt; **bin\/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic**

Okay but now that we have a cluster how can we know which broker is doing what? To see that run the "describe topics" command:

&gt; **bin\/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic**

Topic:my-replicated-topic PartitionCount:1 ReplicationFactor:3 Configs:

Topic: my-replicated-topic Partition: 0 Leader: 1 Replicas: 1,2,0 Isr: 1,2,0

Here is an explanation of output. The first line gives a summary of all the partitions, each additional line gives information about one partition. Since we have only one partition for this topic there is only one line.

* "leader" is the node responsible for all reads and writes for the given partition. Each node will be the leader for a randomly selected portion of the partitions.
* "replicas" is the list of nodes that replicate the log for this partition regardless of whether they are the leader or even if they are currently alive.
* "isr" is the set of "in-sync" replicas. This is the subset of the replicas list that is currently alive and caught-up to the leader.

这里是一个输出的解释。第一行给出了所有的分区的摘要，每一个额外的行给出了一个分区的信息。由于这个主题只有一个分区，所以只有一行。

“leader”是负责为给定的分区所有的读取和写入的节点。每个节点都将是随机选择部分的分区的leader。

“副本”是分区复制日志的节点的列表，无论他们是否是leader，甚至他们目前还活着。

