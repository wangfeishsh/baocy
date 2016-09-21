**ZooKeeper Getting Started Guide**

Getting Started: Coordinating Distributed Applications with ZooKeeper

* Pre-requisites
* Download
* Standalone Operation
* Managing ZooKeeper Storage
* Connecting to ZooKeeper
* Programming to ZooKeeper
* Running Replicated ZooKeeper
* Other Optimizations

**Getting Started: Coordinating Distributed Applications with ZooKeeper**

This document contains information to get you started quickly with ZooKeeper. It is aimed primarily at developers hoping to try it out, and contains simple installation instructions for a single ZooKeeper server, a few commands to verify that it is running, and a simple programming example. Finally, as a convenience, there are a few sections regarding more complicated installations, for example running replicated deployments, and optimizing the transaction log. However for the complete instructions for commercial deployments, please refer to the [ZooKeeper Administrator's Guide](http://zookeeper.apache.org/doc/trunk/zookeeperAdmin.html).

**Pre-requisites**

See [System Requirements](http://zookeeper.apache.org/doc/trunk/zookeeperAdmin.html#sc_systemReq) in the Admin guide.

**Download**

To get a ZooKeeper distribution, download a recent [stable](http://zookeeper.apache.org/releases.html) release from one of the Apache Download Mirrors.

**Standalone Operation**

Setting up a ZooKeeper server in standalone mode is straightforward. The server is contained in a single JAR file, so installation consists of creating a configuration.

Once you've downloaded a stable ZooKeeper release unpack it and cd to the root

To start ZooKeeper you need a configuration file. Here is a sample, create it in **conf\/zoo.cfg**:

`tickTime=2000`

`dataDir=/var/lib/zookeeper`

`clientPort=2181`

This file can be called anything, but for the sake of this discussion call it **conf\/zoo.cfg**. Change the value of **dataDir** to specify an existing \(empty to start with\) directory. Here are the meanings for each of the fields:

**tickTime**

the basic time unit in milliseconds used by ZooKeeper. It is used to do heartbeats and the minimum session timeout will be twice the tickTime.

毫秒是ZooKeeper使用的基本时间单位。它是用来做心跳并且最小会话超时时间是ticktime两倍。

**dataDir**

the location to store the in-memory database snapshots and, unless specified otherwise, the transaction log of updates to the database.

该位置存储内存数据库快照，除非指定其他位置，否则将事务日志记录更新到数据库。

**clientPort**

the port to listen for client connections

Now that you created the configuration file, you can start ZooKeeper:

`bin/zkServer.sh start`

ZooKeeper logs messages using log4j -- more detail available in the [Logging](http://zookeeper.apache.org/doc/trunk/zookeeperProgrammers.html#Logging) section of the Programmer's Guide. You will see log messages coming to the console \(default\) and\/or a log file depending on the log4j configuration.

The steps outlined here run ZooKeeper in standalone mode. There is no replication, so if ZooKeeper process fails, the service will go down. This is fine for most development situations, but to run ZooKeeper in replicated mode, please see [Running Replicated ZooKeeper](http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html#sc_RunningReplicatedZooKeeper).

**Managing ZooKeeper Storage**

For long running production systems ZooKeeper storage must be managed externally \(dataDir and logs\). See the section on [maintenance](http://zookeeper.apache.org/doc/trunk/zookeeperAdmin.html#sc_maintenance) for more details.

**Connecting to ZooKeeper**

`$ bin/zkCli.sh -server 127.0.0.1:2181`

This lets you perform simple, file-like operations.

Once you have connected, you should see something like:

`Connecting to localhost:2181`

`log4j:WARN No appenders could be found for logger (org.apache.zookeeper.ZooKeeper).`

`log4j:WARN Please initialize the log4j system properly.`

`Welcome to ZooKeeper!`

`JLine support is enabled`

`[zkshell: 0]`

