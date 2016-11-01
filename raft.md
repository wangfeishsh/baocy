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

**The leader begins sending out \*\***_Append Entries_**\*\* messages to its followers.**

These messages are sent in intervals specified by the heartbeat timeout.

Followers then respond to each _Append Entries_ message.

This election term will continue until a follower stops receiving heartbeats and becomes a candidate.（这句话的意思是假如leader没有down掉，则follower会一直接收心跳信息，假如leader down掉，它变成一个候选者）

**Let's stop the leader and watch a re-election happen.**

Node A is now leader of term 2.

Requiring a majority of votes guarantees that only one leader can be elected per term.

If two nodes become candidates at the same time then a split vote can occur.

**Let's take a look at a split vote example...**

Two nodes both start an election for the same term...

...and each reaches a single follower node before the other.

Now each candidate has 2 votes and can receive no more for this term.（投票者投给自己一票，只有follower有投票权）

The nodes will wait for a new election and try again.

Node D received a majority of votes in term 5 so it becomes leader.

### Log Replication

Once we have a leader elected we need to replicate all changes to our system to all nodes.

This is done by using the same _Append Entries_ message that was used for heartbeats.

Let's walk through the process.

**First a client sends a change to the leader.**

The change is appended to the leader's log...

...then the change is sent to the followers on the next heartbeat.

An entry is committed once a majority of followers acknowledge it...

...and a response is sent to the client.

**Now let's send a command to increment the value by "2".**

Our system value is now updated to "7".

**Raft can even stay consistent in the face of network partitions.**

Let's add a partition to separate A & B from C, D & E.

Because of our partition we now have two leaders in different terms.

Let's add another client and try to update both leaders.

One client will try to set the value of node B to "3".

Node B cannot replicate to a majority so its log entry stays uncommitted.

The other client will try to set the value of node E to "8".

This will succeed because it can replicate to a majority.

Now let's heal the network partition.

**Node B will see the higher election term and step down.**

Both nodes A & B will roll back their uncommitted entries and match the new leader's log.

Our log is now consistent across our cluster.

