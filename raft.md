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

**That's the problem of \*\***_distributed consensus_**\*\*.**

_Raft_ is a protocol for implementing distributed consensus.

Let's look at a high level overview of how it works.

**A node can be in 1 of 3 states:**

**The \*\***_Follower_**\*\* state,**

**the \*\***_Candidate_**\*\* state,**

**or the \*\***_Leader_**\*\* state.**

All our nodes start in the follower state.

**If followers don't hear from a leader then they can become a candidate.**

**The candidate then requests votes from other nodes.**

Nodes will reply with their vote.

The candidate becomes the leader if it gets votes from a majority of nodes.

This process is called _Leader Election_.

**All changes to the system now go through the leader.**

Each change is added as an entry in the node's log.

This log entry is currently uncommitted so it won't update the node's value.

To commit the entry the node first replicates it to the follower nodes...

then the leader waits until a majority of nodes have written the entry.

The entry is now committed on the leader node and the node state is "5".

The leader then notifies the followers that the entry is committed.

The cluster has now come to consensus about the system state.

This process is called _Log Replication_.

### Leader Election

In Raft there are two timeout settings which control elections.

**First is the election timeout.**

The election timeout is the amount of time a follower waits until becoming a candidate.

The election timeout is randomized to be between 150ms and 300ms.

After the election timeout the follower becomes a candidate and starts a new _election term_...

**...votes for itself...**

...and sends out _Request Vote_ messages to other nodes.

If the receiving node hasn't voted yet in this term then it votes for the candidate...

**...and the node resets its election timeout.**

Once a candidate has a majority of votes it becomes leader.

**The leader begins sending out ****_Append Entries_**** messages to its followers.**

These messages are sent in intervals specified by the heartbeat timeout.

Followers then respond to each _Append Entries_ message.

This election term will continue until a follower stops receiving heartbeats and becomes a candidate.

**Let's stop the leader and watch a re-election happen.**

