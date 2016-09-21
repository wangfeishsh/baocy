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

From the shell, type help to get a listing of commands that can be executed from the client, as in:

`[zkshell: 0] help`

`ZooKeeper host:port cmd args`

`get path [watch]`

`ls path [watch]`

`set path data [version]`

`delquota [-n|-b] path`

`quit`

`printwatches on|off`

`create path data acl`

`stat path [watch]`

`listquota path`

`history`

`setAcl path acl`

`getAcl path`

`sync path`

`redo cmdno`

`addauth scheme auth`

`delete path [version]`

`deleteall path`

`setquota -n|-b val path`

From here, you can try a few simple commands to get a feel for this simple command line interface. First, start by issuing the list command, as in ls, yielding:

`[zkshell: 8] ls /`

`[zookeeper]`

Next, create a new znode by running create \/zk\_test my\_data. This creates a new znode and associates the string "my\_data" with the node. You should see:

`[zkshell: 9] create /zk_test my_data`

`Created /zk_test`

Issue another ls \/ command to see what the directory looks like:

`[zkshell: 11] ls /`

`[zookeeper, zk_test]`

Notice that the zk\_test directory has now been created.

Next, verify that the data was associated with the znode by running the get command, as in:

`[zkshell: 12] get /zk_test`

`my_data`

`cZxid = 5`

`ctime = Fri Jun 05 13:57:06 PDT 2009`

`mZxid = 5`

`mtime = Fri Jun 05 13:57:06 PDT 2009`

`pZxid = 5`

`cversion = 0`

`dataVersion = 0`

`aclVersion = 0`

`ephemeralOwner = 0`

`dataLength = 7`

`numChildren = 0`

We can change the data associated with zk\_test by issuing the set command, as in:

`[zkshell: 14] set /zk_test junk`

`cZxid = 5`

`ctime = Fri Jun 05 13:57:06 PDT 2009`

`mZxid = 6`

`mtime = Fri Jun 05 14:01:52 PDT 2009`

`pZxid = 5`

`cversion = 0`

`dataVersion = 1`

`aclVersion = 0`

`ephemeralOwner = 0`

`dataLength = 4`

`numChildren = 0`

`[zkshell: 15] get /zk_test`

`junk`

`cZxid = 5`

`ctime = Fri Jun 05 13:57:06 PDT 2009`

`mZxid = 6`

`mtime = Fri Jun 05 14:01:52 PDT 2009`

`pZxid = 5`

`cversion = 0`

`dataVersion = 1`

`aclVersion = 0`

`ephemeralOwner = 0`

`dataLength = 4`

`numChildren = 0`

\(Notice we did a get after setting the data and it did, indeed, change.

Finally, let's delete the node by issuing:

`[zkshell: 16] delete /zk_test`

`[zkshell: 17] ls /`

`[zookeeper]`

`[zkshell: 18]`

That's it for now. To explore more, continue with the rest of this document and see the [Programmer's Guide](http://zookeeper.apache.org/doc/trunk/zookeeperProgrammers.html).

**Programming to ZooKeeper**

ZooKeeper has a Java bindings and C bindings. They are functionally equivalent. The C bindings exist in two variants: single threaded and multi-threaded. These differ only in how the messaging loop is done. For more information, see the [Programming Examples in the ZooKeeper Programmer's Guide](http://zookeeper.apache.org/doc/trunk/zookeeperProgrammers.html#ch_programStructureWithExample) for sample code using of the different APIs.

**Running Replicated ZooKeeper**

Running ZooKeeper in standalone mode is convenient for evaluation, some development, and testing. But in production, you should run ZooKeeper in replicated mode. A replicated group of servers in the same application is called a _quorum_, and in replicated mode, all servers in the quorum have copies of the same configuration file.

quorum，原指为了处理事务、拥有做出决定的权力而必须出席的众议员或参议员的数量（一般指半数以上）。（最低）法定人数。

_**Note**_

_For replicated mode, a minimum of three servers are required, and it is strongly recommended that you have an odd number of servers. If you only have two servers, then you are in a situation where if one of them fails, there are not enough machines to form a majority quorum. Two servers is inherently __**less**__ stable than a single server, because there are two single points of failure._

