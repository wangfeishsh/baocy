## 1. Getting Started

### 1.1 Introduction

Kafka is a distributed, partitioned, replicated commit log service. It provides the functionality of a messaging system, but with a unique design.

kafka 是一个分布式的，分区的，重复（or复制）的提交日志服务。它使用独特的设计，提供了消息系统的功能。

What does all that mean?

First let's review some basic messaging terminology术语:

* Kafka maintains feeds of messages in categories called _topics_.
* We'll call processes that publish messages to a Kafka topic _producers_.
* We'll call processes that subscribe to topics and process the feed of published messages _consumers_.
* Kafka is run as a cluster comprised of one or more servers each of which is called a _broker_.

