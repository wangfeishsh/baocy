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

毫秒是ZooKeeper使用的基本时间单位。它是用来做什么的心跳和最小会话超时将两倍ticktime。

**dataDir**

the location to store the in-memory database snapshots and, unless specified otherwise, the transaction log of updates to the database.

**clientPort**

the port to listen for client connections

Now that you created the configuration file, you can start ZooKeeper:

