参考：http:\/\/blog.csdn.net\/dc\_726\/article\/details\/48832405  分布式一致性协议Raft原理与实例

```
   http:\/\/thesecretlivesofdata.com\/raft\/   动画版Raft讲解 
```

So What is Distributed Consensus?

Let's start with an example...

**Let's say we have a single node system**

For this example, you can think of our node as a database server that stores a single value.

**We also have a client that can send a value to the server.**

Coming to agreement, or _consensus_, on that value is easy with one node.

**But how do we come to consensus if we have multiple nodes?**

**That's the problem of **_**distributed consensus**_**.**

_Raft_ is a protocol for implementing distributed consensus.

Let's look at a high level overview of how it works.

**A node can be in 1 of 3 states:**

**The **_**Follower**_** state,**

**the **_**Candidate**_** state,**

**or the **_**Leader**_** state.**

All our nodes start in the follower state.

**If followers don't hear from a leader then they can become a candidate.**

**The candidate then requests votes from other nodes.**

Nodes will reply with their vote.

The candidate becomes the leader if it gets votes from a majority of nodes.

This process is called _Leader Election_.

