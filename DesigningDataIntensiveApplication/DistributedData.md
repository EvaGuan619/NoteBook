# 5. Replication
 Reasons to replicate data:
* reduce access latency (geographically close to users)
* increase availability
* increase read throughput
### 5.1 Leaders and followers
* one of the replica is designated the leader
* others are known as followers
* read could accepted on leader and replicas, write only accepted on the leader
build-in feature of many relational databases, some non-relational databases, 
distributed message brokers and some network filesystems and replicated block devices such as DRBD
    #### 5.1.1 Synchronous Versus Asynchronous Replication
    ##### Synchronous
    * Advantage: guaranteed to have an up-to-date copy of the data that is consistent with the leader
    * Disadvantage: if the follower doesn't respond, the write cannot be processed. The leader must block all writes and wait
    ##### Asynchronous
    * Advantage: the leader can continue processing writes, even if all followers have fallen behind
    * Disadvantage: durable, if the leader fails and is not recoverable, any writes that have not yet been replicated to followers are lost
    ###### Chain replication -> Microsoft Azure Storage
    #### 5.1.2 Setting Up New Followers
    1. Take a consistent snapshot of the leader's database at some point in time
    2. Copy the snapshot to the new follower node
    3. The follower connects to the leader and requests all the data changes that have happened since the snapshot was taken
    4. When the follower has processed the backlog of data changes, we say it has caught up
    #### 5.1.3 Handling Node Outages
    ##### How to achieve high-availability?
    * Follower failure: Catch-up recovery
    * Leader failure: Failover
    #### 5.1.4 Implementation of Replication Logs
### 5.2 Problems with Replication Lag
#### 5.2.1 Reading Your Own Writes
With asynchronous replication, if the user views the data shortly after making a write, 
the new data may not yet have reached the replica. To this user, it looks as though the data they submitted was lost.
Mention a few solutions:
* When reading something that the user may have modified, read from the leader.
* If most things in application are potentially editable by the user, use other criteria.
* The client can remember the timestamp of its most recent write
#### 5.2.2 Monotonic Reads
After users have seen the data at one point in time, they shouldn't later see the data from some earlier point in time.
#### 5.2.3 Consistent Prefix Reads
Users should see the data in a state that makes causal sense: for example, seeing a question and its reply in the same order.
#### 5.2.4 Solutions for Replication Lag
### 5.3 Multi-Leader Replication
Clients send each write to one of several leader nodes, any of which can accept writes.
The leaders send streams of data change events to each other and to any follower nodes.
#### 5.3.1 Use Cases for Multi-Leader Replication
Rarely makes sense within a single datacenter. However,
* multi-datacenter operation
* client with offline operation (e.g. git)
* collaborative editing (e.g. google doc, shopping cart)
#### 5.3.2 Handling Write Conflicts
#### 5.3.3 Multi-Leader Replication Topologies
### 5.4 Leaderless Replication
Clients send each write to several nodes, and read from several nodes in parallel in order to detect and correct nodes with stale data.
#### 5.4.1 Writing to the Database When a Node Is Down
* read repair and anti-entropy
* quorums for reading and writing ----> (w + r > n)
#### 5.4.2 Limitations of Quorum Consistency
if consider write failed, no roll-back
#### 5.4.3 Sloppy Quorums and Hinted Handoff
#### 5.4.4 Detecting Concurrent Writes
Dynamo-style databases allow several clients to concurrently write to the same key, which means that conflicts will occur even if strict quorums are used (no well-defined ordering).
### 5.5 Summary

# 6. Partitioning
### 6.1 Partitioning and Replication
### 6.2 Partitioning of Key-Value Data
### 6.3 Partitioning and Secondary Indexes
### 6.4 Rebalancing Partitions
### 6.5 Request Routing
### 6.6 Summary